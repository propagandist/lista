# 最短の起動

Docker Compose で Lista を起動し、最初の利用者でログインするところまでを進めます。最初は手元の PC（`http://localhost:8080`）で試し、動いたら [リバースプロキシと https](reverse-proxy.md) で利用者が開ける形にします。

## 1. ログインのプロバイダに Lista を登録する

使う IdP に、Lista をアプリ（OIDC のクライアント）として登録します。手順は [ログインの設定](oidc.md) にあります。ここで決める値は次の 4 つです。

| 値 | 手元で試すときの例 |
|---|---|
| リダイレクト URI | `http://localhost:8080/login/oauth2/code/oidc` |
| issuer の URI | `https://login.microsoftonline.com/<テナント ID>/v2.0` |
| クライアント ID | IdP が発行した値 |
| クライアントシークレット | IdP が発行した値 |

## 2. ファイルを置く

空のフォルダーを作り、次の 2 つを置きます。

- [compose.yaml](../files/compose.yaml)
- [lista.env.example](../files/lista.env.example) を `.env` という名前で写したもの

```bash
mkdir lista && cd lista
# compose.yaml と lista.env.example をここへ置いてから
cp lista.env.example .env
chmod 600 .env
```

## 3. `.env` に値を入れる

必須の値を入れます。

```bash
LISTA_VERSION=<使う版>
DB_PASSWORD=<長いランダムな文字列>
LISTA_BASE_URL=http://localhost:8080
LISTA_OIDC_ISSUER_URI=<issuer の URI>
LISTA_OIDC_CLIENT_ID=<クライアント ID>
LISTA_OIDC_CLIENT_SECRET=<クライアントシークレット>
LISTA_INITIAL_ACCOUNT_EMAIL=<最初にログインする人のメールアドレス>
```

- `LISTA_VERSION` には、Releases に出ている版を入れます
- Microsoft Entra ID のときは、`LISTA_OIDC_USERNAME_AS_EMAIL=true` も入れます（[ログインの設定](oidc.md)）
- `LISTA_INITIAL_ACCOUNT_EMAIL` は、利用者が 1 人もいないときだけ使われます。2 人目からは画面で登録します
- ほかの項目は [設定の一覧](configuration.md) にあります

## 4. 起動する

```bash
docker compose up -d
docker compose logs -f webapp
```

次の 2 行がログに出れば、起動は済んでいます。`Ctrl+C` でログの表示を止めます（Lista は止まりません）。

```
Started WebApplicationKt in ... seconds
[initial-account] created: id=1
```

初めての起動では、その前に DB の表を作るログ（`Migrating schema "public" ...`）が並びます。

起動しないときは、[困ったとき](troubleshooting.md) を見てください。

## 5. ログインする

ブラウザで `http://localhost:8080/` を開き、「ログイン」を押します。IdP でサインインすると、ホームが開きます。

ログインできたら、次の順に進めます。

1. 「利用者」の画面で、使う人のメールアドレスを登録します。**登録した人だけがログインできます**
2. 「テンプレート」の画面で、毎月の定例タスクを登録します。今月のシートは、ホームか月の記録を開いたときに作られます
3. 通知を使うなら、[通知の設定](notifications.md) を進めます

画面の使い方は [利用者ガイド](../user/overview.md) にあります。

## 6. 利用者が開ける形にする

手元の `http://localhost:8080` は、そのホストのブラウザからしか開けません。利用者が開けるようにするには、次の 2 つが要ります。

1. [リバースプロキシと https](reverse-proxy.md) で、`https://lista.example.com` のような URL を用意する
2. IdP のリダイレクト URI に `https://lista.example.com/login/oauth2/code/oidc` を足し、`.env` の `LISTA_BASE_URL` をその URL に変えて、`docker compose up -d` で作り直す

## 止める・消す

```bash
docker compose stop       # 止める（データは残る）
docker compose down       # コンテナを消す（データは名前つきボリュームに残る）
docker compose down -v    # データごと消す。元に戻せません
```
