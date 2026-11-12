# 困ったとき（管理者向け）

まずログを見てください。compose のフォルダーで次を打つと、Lista のログが出ます。

```bash
docker compose logs --tail 200 webapp
```

利用者からの問い合わせ（チェックできない、通知が来ないなど）は、[利用者ガイドの困ったとき](../user/faq.md) も見てください。

## 起動しない

### `required variable ... is missing a value` と出て、何も起動しない

`.env` の必須の値が空です。メッセージに出た変数を入れてください（[設定の一覧](configuration.md)）。

### `Unable to resolve Configuration with the provided Issuer of "..."`

Lista が、起動のときに IdP の設定を取れていません。次を確かめてください。

- `LISTA_OIDC_ISSUER_URI` の綴り。`<issuer>/.well-known/openid-configuration` をホストで開けるか
- Lista のコンテナから IdP へ出られるか（プロキシ・ファイアウォール・名前解決）。ログの下のほうの `Caused by:` に、`UnknownHostException`（名前を引けない）や接続の失敗が出ます
- 取ってきた設定の `issuer` が、`LISTA_OIDC_ISSUER_URI` と一字一句同じか（末尾の `/` も含めて）

### `password authentication failed for user "lista"`

`.env` の `DB_PASSWORD` が、DB を作ったときの値と違います。DB のパスワードは最初の起動のときに決まり、後から `.env` を変えても DB の側は変わりません。元の値に戻すか、DB の側で `ALTER ROLE lista PASSWORD '...'` を打ってください。

### `Migration ... failed` や `Validate failed`

DB の表の変更が当たりませんでした。メッセージを控え、[バックアップと復元](backup.md) で版上げの前の dump から戻して、前の版で起動してください。`LISTA_FLYWAY_IGNORE_MIGRATION_PATTERNS` を変えていれば、既定に戻してください。

### コンテナが何度も再起動する

`docker compose ps` で状態を見て、`docker compose logs webapp` の最初の `APPLICATION FAILED TO START` か `Caused by:` を探してください。`OutOfMemoryError` なら、`JAVA_TOOL_OPTIONS` の `-Xmx` を上げます。

## ログインできない

### ログインのボタンを押すと、IdP がエラーを出す

IdP に登録したリダイレクト URI と、Lista が送った戻り先が食い違っています（Microsoft Entra ID なら `AADSTS50011`、Keycloak なら `Invalid parameter: redirect_uri`）。

- IdP に `<LISTA_BASE_URL>/login/oauth2/code/oidc` が登録されているか
- リバースプロキシが `X-Forwarded-Proto` と `Host`（か `X-Forwarded-Host`）を渡しているか。渡していないと、戻り先が `http://127.0.0.1:8080/...` になります（[リバースプロキシと https](reverse-proxy.md)）

### 「ログインに失敗しました。登録されていないアカウントか、無効になっています。」

IdP でのサインインはできていますが、Lista が通していません。

- 「利用者」の画面に、その人のメールアドレスが登録され、有効になっているか
- IdP が ID トークンに `email` を入れているか。Microsoft Entra ID なら `LISTA_OIDC_USERNAME_AS_EMAIL=true` にし、UPN を登録します
- IdP の側でメールアドレスを確かめ済み（`email_verified`）になっているか
- 利用者が 1 人もいない DB なら、`LISTA_INITIAL_ACCOUNT_EMAIL` を入れて起動したか。ログに `[initial-account] created: id=1` が出ます。`[initial-account] skipped: accounts is not empty.` なら、すでに利用者がいます

### ログインしても、すぐにログインの画面へ戻る

http で開いています。Lista の Cookie は https（と `localhost`）でしか送られません。https で開いてください。

## 通知が届かない

まず、その人の「自分の設定」で「テスト送信」を押し、画面に出る結果を見てください。

- 「送り先が分かりません。…」: IP メッセンジャーの名前が登録されていないか、在席が見つかっていません。「利用者」の画面で「在席」になっているかを見ます
- 「通知先の名前を引けないか、ネットワークに問題があります。」: 固定の通知先のホスト名を引けないか、送れていません
- 「ntfy のサーバーに届かないか、トークンが違います。」: `LISTA_NTFY_BASE_URL` と `LISTA_NTFY_TOKEN` を確かめます（[通知の設定](notifications.md)）

IP メッセンジャーで誰も「在席」にならないとき:

- `compose.ipmsg.yaml` を重ねて起動しているか。ログに `IPメッセンジャーの待ち受けを始めました. (port = 2425)` が出るか
- `IPメッセンジャーのポートを開けません.` と出るなら、ホストの UDP 2425 をほかのものが使っています
- ホストと各 PC のファイアウォールで UDP 2425 を許可しているか
- ブロードキャストがルーターを越えていないか。`LISTA_IPMSG_BROADCAST` にセグメントのアドレスを入れます

## そのほか

### 画面の見た目が崩れる、ボタンが動かない

利用者の端末から、画面の部品の配信元（`cdn.jsdelivr.net`・`cdnjs.cloudflare.com`）へ出られていない可能性があります（[データの送り先](data-flows.md)）。

### 今月のシートが無い

シートは毎月 1 日の 0:05 に作ります。その時刻に Lista が止まっていても、ホーム・月の記録・カレンダーで当月を開いたときに作ります。作ったときはログに `Generated monthly sheet.` が出ます。

### 動いている版を知りたい

画面のいちばん下（フッター）に出ます。イメージの版は `docker image inspect ghcr.io/propagandist/lista:<版> --format '{{ index .Config.Labels "org.opencontainers.image.version" }}'` でも見られます。
