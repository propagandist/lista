# ログインの設定

Lista は、OpenID Connect（OIDC）のプロバイダ（IdP）1 つでログインします。パスワードは Lista では持ちません。

## ログインの決まり

- **ログインできるのは、「利用者」の画面に登録したメールアドレスの人だけです。** IdP にサインインできても、登録が無いか無効になっている人は「ログインに失敗しました。登録されていないアカウントか、無効になっています。」に戻ります
- メールアドレスは、IdP が ID トークンに入れる `email` クレームから取ります。大文字と小文字は区別しません
- `email_verified` が `false` の人は拒みます。メールアドレスを確かめていない人が、他人の登録になりすませないためです。クレームが無ければ通します
- `email` が無いとき、`LISTA_OIDC_USERNAME_AS_EMAIL=true` なら `preferred_username` をメールアドレスとして使います。**Microsoft Entra ID のときだけ** `true` にしてください。ほかの IdP では、`preferred_username` がメールアドレスとは限りません
- 氏名は `name`（無ければ `given_name`、それも無ければメールアドレスの `@` より前）から取り、ログインのたびに書き換えます
- Lista は UserInfo のエンドポイントへは問い合わせません。必要なクレームを ID トークンに入れてください
- ログアウトは Lista の側だけです。IdP のサインインは残ります

## IdP に登録する値

| 項目 | 値 |
|---|---|
| アプリの種類 | Web（機密クライアント。クライアントシークレットを使う） |
| リダイレクト URI | `<LISTA_BASE_URL>/login/oauth2/code/oidc`（例: `https://lista.example.com/login/oauth2/code/oidc`） |
| 要求するスコープ | `openid`・`email`・`profile` |
| 許可の種類 | Authorization Code |

`.env` には次の 3 つを入れます。

| 変数 | 値 |
|---|---|
| `LISTA_OIDC_ISSUER_URI` | IdP の issuer。`<issuer>/.well-known/openid-configuration` が開けて、中の `issuer` と一字一句同じであること |
| `LISTA_OIDC_CLIENT_ID` | クライアント ID |
| `LISTA_OIDC_CLIENT_SECRET` | クライアントシークレット |

ログインのボタンの文言は `LISTA_OIDC_LOGIN_LABEL` で変えられます（例: `Microsoft アカウントでログイン`）。

- Lista は起動のときに issuer へ問い合わせます。ホストから IdP へ https で出られる必要があります
- **クライアントシークレットには期限があることが多くあります。** 切れると全員がログインできなくなります。期限を控え、切れる前に作り直してください

## Microsoft Entra ID の例

1. Microsoft Entra 管理センターの「アプリの登録」で、新しい登録を作ります
   - サポートされるアカウントの種類: 「この組織ディレクトリのみ」
   - リダイレクト URI: 「Web」で `https://lista.example.com/login/oauth2/code/oidc`
2. 「証明書とシークレット」でクライアントシークレットを作り、値を控えます（画面を離れると二度と表示されません）
3. 「API のアクセス許可」は既定の `User.Read`（委任）だけで足ります。アプリケーションの許可は付けません。**管理者の同意を与えてください。** 与えないと、各自の初回のログインで同意の画面が出ます。テナントが利用者の同意を禁じていれば、その人はログインできません
4. `.env` に入れます

   ```bash
   LISTA_OIDC_ISSUER_URI=https://login.microsoftonline.com/<テナント ID（GUID）>/v2.0
   LISTA_OIDC_CLIENT_ID=<アプリケーション（クライアント）ID>
   LISTA_OIDC_CLIENT_SECRET=<クライアントシークレットの値>
   LISTA_OIDC_USERNAME_AS_EMAIL=true
   LISTA_OIDC_LOGIN_LABEL=Microsoft アカウントでログイン
   ```

- issuer はテナント ID（GUID）で書きます。`common`・`organizations`・ドメイン名では起動に失敗します
- Entra は `localhost` 以外の `http://` のリダイレクト URI を受け付けません。利用者が開く URL は https にしてください
- メール属性が空のメンバーは `email` クレームが出ません。そのため `LISTA_OIDC_USERNAME_AS_EMAIL=true` で UPN（`preferred_username`）を使います。「利用者」の画面には UPN を登録してください

## Keycloak の例

1. realm を作り、その中にクライアントを作ります
   - Client authentication: On（機密クライアント）
   - Standard flow: On
   - Valid redirect URIs: `https://lista.example.com/login/oauth2/code/oidc`
2. 「Credentials」のクライアントシークレットを控えます
3. 利用者にはメールアドレスを入れ、「Email verified」をオンにします（オフだと Lista が拒みます）
4. `.env` に入れます

   ```bash
   LISTA_OIDC_ISSUER_URI=https://<Keycloak のホスト>/realms/<realm>
   LISTA_OIDC_CLIENT_ID=<クライアント ID>
   LISTA_OIDC_CLIENT_SECRET=<クライアントシークレット>
   ```

- Keycloak のホスト名の設定（`--hostname`）で、issuer の URL が決まります。Lista のコンテナから見た URL と、利用者のブラウザから見た URL が同じになるようにしてください。違うと、起動時の issuer の照合か、ログインのどちらかで失敗します
