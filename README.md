# lista

毎月くり返す事務作業を、テンプレートから月ごとのチェックシートにして、チームで消し込む Web アプリ（配布用のコンテナイメージ）。

## これは何か

勤怠の締め・請求書の発行・給与の振込のように、毎月決まった時期に来る作業を、テンプレートから月ごとに自動で作ります。担当者・期限・サブタスク（1 階層）・メモ・添付を、チームの全員が同じ画面で見て消し込めます。月に属さない突発の作業も同じ一覧に並び、月の記録は PDF で残せます。

このリポジトリにあるのは、コンテナで動かすための compose とマニュアルだけです。ソースコードは公開していません。イメージは `ghcr.io/propagandist/lista` から取得します（`linux/amd64`・`linux/arm64`）。

## なぜ作ったか

株式会社 PROPAGANDIST の社内では、毎月の事務作業を 1 枚の Excel で管理していました。毎月シートを手で複製し、月末・月初の期限を目で探していました。Excel は 1 人が手元で持つ前提なので、誰が担当で、いつ誰が済ませたかを共有できません。突発の作業は表に入らず、毎月の作業と混ざっていました。

- **汎用のタスク管理（スプレッドシート・チャットのタスク機能など）を選ばなかった**: 「毎月くり返す作業を、月ごとの記録として残す」形を持たず、結局テンプレートの複製を人が続けることになるため
- **定例と突発を 1 つの一覧にした**: 担当・チェック・通知の扱いを 1 本にし、「今やるべきもの」を期限で区切って 1 画面に出すため
- **自分たちのサーバーの中で動かす前提にした**: 事務作業の件名や取引先名を、社外のサービスへ預けずに済ませるため

社内で使っているものを、同じ悩みを持つ組織にも使ってもらえるよう、イメージを配っています。

## 使い方

用意するものは、Docker（Compose v2）と、OpenID Connect のログインのプロバイダ（Microsoft Entra ID・Keycloak など）です。Lista はパスワードを持たず、ログインをプロバイダに任せます。

1. プロバイダに Lista をクライアントとして登録します。手元で試すなら、リダイレクト URI は `http://localhost:8080/login/oauth2/code/oidc` です（[ログインの設定](docs/manual/admin/oidc.md)）
2. 次のとおり起動します

```sh
git clone https://github.com/propagandist/lista.git
cd lista
cp lista.env.example .env
chmod 600 .env
# .env を開き、LISTA_VERSION・DB_PASSWORD・LISTA_BASE_URL・LISTA_OIDC_*・LISTA_INITIAL_ACCOUNT_EMAIL を入れる
docker compose up -d
docker compose logs -f webapp   # 「Started WebApplicationKt」が出れば起動済み
```

3. 同じマシンのブラウザで http://localhost:8080/ を開き、`LISTA_INITIAL_ACCOUNT_EMAIL` の人でログインします

`LISTA_VERSION` には、[Releases](https://github.com/propagandist/lista/releases) に出ている版を入れます。

ほかの人の PC から使うときは、https のリバースプロキシを前に置きます。ログインの Cookie に `Secure` を付けているので、`localhost` 以外の http ではログインできません。詳しい手順はマニュアルにあります。

- [導入・運用ガイド](docs/manual/README.md): 最短の起動・ログインの設定・リバースプロキシ・通知（IP メッセンジャー・ntfy）・Outlook アドイン・バックアップ・版上げ・困ったとき
- [利用者ガイド](docs/manual/user/overview.md): 画面の使い方

同じマニュアルは、動いている Lista の「使い方」からも読めます。

## 外へ送るもの

**Lista は、配布元（株式会社 PROPAGANDIST）へは何も送りません。** 利用状況の送信や、更新の確認もしません。送り先は、導入した組織が設定したものか、利用者のブラウザが読みに行くものだけです。一覧は [データの送り先](docs/manual/admin/data-flows.md) にあります。

| 送り先 | いつ | 送るもの |
|---|---|---|
| ログインのプロバイダ（`LISTA_OIDC_ISSUER_URI`） | 起動のときとログインのたび | ログインの手続きに要る値 |
| `api.anthropic.com`（Anthropic PBC・米国） | `ANTHROPIC_API_KEY` を入れ、Outlook のメールから追加画面を開いたときだけ | メールの件名・差出人の表示名（無ければメールアドレス）・受信日時・本文の先頭 30,000 字。宛先・CC・添付ファイルは送りません。鍵が空なら何も送りません |
| ntfy のサーバー（`LISTA_NTFY_BASE_URL`） | 設定したときだけ。朝のまとめ・担当の知らせ | 通知の本文（タスク名・相手・期限など） |
| 同じ LAN の IP メッセンジャー | 設定したときだけ（同梱の compose の既定では送りません） | 通知の本文と、在席の確認 |

利用者のブラウザは、画面の部品を `cdn.jsdelivr.net`・`cdnjs.cloudflare.com`・`appsforoffice.microsoft.com`（Outlook アドインの中だけ）から読みます。業務のデータは送りません。

## 技術スタック

- アプリ: Kotlin・Spring Boot 3・Thymeleaf・jOOQ・Flyway（Java 17 の JRE で動く）
- DB: PostgreSQL 18
- ログイン: OpenID Connect（Spring Security の OAuth2 Client）
- PDF: Typst（日本語フォントは BIZ UDPGothic）
- AI: Claude API（Anthropic の Java SDK）
- イメージ: `eclipse-temurin:17-jre-noble`（Ubuntu 24.04）の上に、実行可能 jar と Typst の CLI を置いている

画面・通知・マニュアルは日本語だけです。時刻は日本時間（`Asia/Tokyo`）で扱います。

## ライセンス / 位置づけ

株式会社 PROPAGANDIST が社内で使っている Lista を、コンテナイメージとして配っているものです。オープンソースではありません。

- イメージとこのリポジトリのファイルは、[LICENSE](LICENSE) の条件で使えます。自分の組織の中でなら、商用を含めて無償で使えます。組織の外への再配布はできません
- イメージに含まれる第三者のソフトウェアは、それぞれのライセンスに従います。一覧は [THIRD-PARTY-NOTICES.md](THIRD-PARTY-NOTICES.md) にあります
- 版ごとの確かめの手順は [docs/release-check.md](docs/release-check.md) にあります
- Issue は受け付けますが、対応は保証しません
