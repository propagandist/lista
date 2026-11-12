# 設定の一覧

`.env` に書いて、同梱の [compose.yaml](../files/compose.yaml) から Lista へ渡す値です。変えたら `docker compose up -d` で作り直してください。

## 必須

| 変数 | 意味 |
|---|---|
| `LISTA_VERSION` | 使う版（イメージ `ghcr.io/propagandist/lista` のタグ） |
| `DB_PASSWORD` | PostgreSQL のパスワード。初めて起動したときに DB が作られます。後から変えるときは、DB の側も `ALTER ROLE` で変えます |
| `LISTA_BASE_URL` | 利用者が開く URL（末尾の `/` は付けない）。通知の本文・ntfy の通知を押したときの行き先・Outlook アドインのマニフェストに入ります |
| `LISTA_OIDC_ISSUER_URI` | ログインのプロバイダの issuer（[ログインの設定](oidc.md)） |
| `LISTA_OIDC_CLIENT_ID` | クライアント ID |
| `LISTA_OIDC_CLIENT_SECRET` | クライアントシークレット |

## 最初だけ

| 変数 | 既定 | 意味 |
|---|---|---|
| `LISTA_INITIAL_ACCOUNT_EMAIL` | 空 | 利用者が 1 人もいないときだけ、起動のときにこのメールアドレスの人を登録します。利用者がいれば何もしません。登録できたら空に戻してかまいません |

## ログイン

| 変数 | 既定 | 意味 |
|---|---|---|
| `LISTA_OIDC_LOGIN_LABEL` | `ログイン` | ログインのボタンの文言 |
| `LISTA_OIDC_USERNAME_AS_EMAIL` | `false` | `email` クレームが無いとき、`preferred_username` をメールアドレスとして使います。Microsoft Entra ID のときだけ `true` |

## 通知

| 変数 | 既定 | 意味 |
|---|---|---|
| `COMPOSE_PROFILES` | 空 | `ntfy` にすると、同梱の ntfy のサーバーを起動します |
| `LISTA_NTFY_BASE_URL` | 空 | Lista が ntfy へ送るときの URL。空なら ntfy へは送らず、「自分の設定」にも出しません。同梱の ntfy なら `http://ntfy` |
| `LISTA_NTFY_PUBLIC_URL` | 空 | 利用者が購読に使う URL（https）。画面に出します。空なら `LISTA_NTFY_BASE_URL` |
| `LISTA_NTFY_TOKEN` | 空 | ntfy へ送るときのアクセストークン（`tk_` で始まる） |
| `LISTA_IPMSG_BROADCAST` | `255.255.255.255` | IP メッセンジャーの在席の確認を送るアドレス。カンマ区切りで複数。`compose.ipmsg.yaml` を重ねたときだけ使います |
| `LISTA_DB_PORT` | `15432` | `compose.ipmsg.yaml` を重ねたとき、DB をホストの 127.0.0.1 に出すポート |

## AI

| 変数 | 既定 | 意味 |
|---|---|---|
| `ANTHROPIC_API_KEY` | 空 | Claude API の鍵。空なら AI を呼びません（[AI の下書き](ai.md)） |

## 動かし方

| 変数 | 既定 | 意味 |
|---|---|---|
| `LISTA_PORT` | `8080` | Lista を出すホストのポート（127.0.0.1 の上） |
| `LISTA_NTFY_PORT` | `2586` | 同梱の ntfy を出すホストのポート（127.0.0.1 の上） |
| `JAVA_TOOL_OPTIONS` | `-Xms128m -Xmx384m -XX:+UseSerialGC` | JVM の設定。メモリが足りなければ `-Xmx` を上げます |

## 変えられないもの

次の値は Lista の中で決まっていて、上の変数では変えられません。

| 項目 | 値 |
|---|---|
| 時刻 | 日本時間（Asia/Tokyo） |
| 画面と通知の言葉 | 日本語 |
| 朝のまとめを送る日 | 月〜金（祝日を区別しない）。6:00 より前には送りません |
| 朝のまとめに載せる期限 | 期限切れ・今日・3 日後まで |
| 在席の確認 | 8〜18 時の毎時 0 分 |
| ntfy の朝のまとめ | 月〜金の 8:30〜18:30 の毎時 30 分 |
| 毎月のシートの作成 | 毎月 1 日の 0:05（逃しても、ホームなどを開いたときに作ります） |
| 添付の上限 | 1 ファイル 10 MB |
| 月の記録の PDF | 同時に作るのは 2 つまで。30 秒で打ち切ります |
| ログインのセッション | 操作が無いまま 3 時間で切れます |
| AI のモデル | `claude-opus-5-5` |

`LISTA_FLYWAY_IGNORE_MIGRATION_PATTERNS` は、DB の移行の検証で見逃すものを決める値です。既定（`*:future`）のままにしてください（[版上げ](upgrade.md)）。
