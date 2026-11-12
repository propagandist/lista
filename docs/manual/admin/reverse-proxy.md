# リバースプロキシと https

利用者は Lista を https で開きます。Lista の前に、同じホストでリバースプロキシを置いてください。

## https が要る理由

- Lista のログインの Cookie には、常に `Secure` を付けています。ブラウザは、`localhost` 以外の `http://` ではこの Cookie を送りません。http のままだと、ログインしても画面に戻るたびにログインを繰り返します
- 多くの IdP は、`localhost` 以外の `http://` をリダイレクト URI に登録させません（Microsoft Entra ID もそうです）
- Outlook のアドインは https のページしか開けません

証明書は、利用者のブラウザが信頼するものにしてください。社内だけで使う場合も同じです。

## リバースプロキシに求めること

| 項目 | 値 |
|---|---|
| 受ける側 | `https://lista.example.com`（443） |
| 転送先 | `http://127.0.0.1:8080`（`.env` の `LISTA_PORT` を変えたらそのポート） |
| 渡すヘッダー | `X-Forwarded-Proto`（`https`）と、`Host` か `X-Forwarded-Host`（利用者が開いたホスト名） |
| アップロードの上限 | 11 MB 以上（添付は 1 ファイル 10 MB まで） |

- Lista は `X-Forwarded-*` を見て、ログインの戻り先（リダイレクト URI）を組み立てます。渡さないと、戻り先が `http://127.0.0.1:8080/...` になり、IdP がログインを拒みます
- **8080 を LAN やインターネットへ出さないでください。** Lista は `X-Forwarded-*` を誰から届いたものでも信じます。8080 に直接届くと、https を通らずに使われ、ヘッダーも偽れます。同梱の compose は `127.0.0.1` にだけ出しています
- WebSocket の設定は、Lista には要りません。ntfy をリバースプロキシの後ろに置くときは要ります（[通知の設定](notifications.md)）

## 設定の例

どちらも、証明書の取得と更新はリバースプロキシ側の仕組みで行う前提です。配布元では、どちらの例でもログインの戻り先が https で組み立てられることを確かめました（2026-11-11。Caddy 2.10・nginx 1.29）。

### Caddy

```
lista.example.com {
    reverse_proxy 127.0.0.1:8080
}
```

Caddy は `X-Forwarded-Proto` と `X-Forwarded-Host` を自分で付けます。

### nginx

```nginx
server {
    listen 443 ssl;
    server_name lista.example.com;
    ssl_certificate     /etc/ssl/lista.example.com/fullchain.pem;
    ssl_certificate_key /etc/ssl/lista.example.com/privkey.pem;

    client_max_body_size 11m;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

443 以外のポートで受けるときは、`Host $host` を `Host $http_host` にしてください。`$host` はポートを含まないので、ログインの戻り先からポートが落ちます。

## 変えたあとにすること

1. `.env` の `LISTA_BASE_URL` を、利用者が開く URL（例: `https://lista.example.com`）にします。通知の本文と、Outlook アドインのマニフェストに入る URL です
2. IdP のリダイレクト URI に `https://lista.example.com/login/oauth2/code/oidc` を足します
3. `docker compose up -d` で作り直します

## 確かめ方

- `curl https://lista.example.com/actuator/health` が `{"status":"UP"}` を返します（`-k` なしで通ること）
- ブラウザでログインでき、ホームが開きます
- LAN の別の PC から `http://<ホストの IP>:8080/` に繋がらないこと
