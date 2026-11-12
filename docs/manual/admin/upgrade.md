# 版上げ

新しい版は Releases に出ます。変わったことは各版のノートに書いてあります。

## 手順

1. Releases のノートを読みます。設定（`.env`）を足すものや、手順が要るものはノートに書いてあります
2. `.env` の `LISTA_VERSION` を新しい版に書き換え、先にイメージを取ってきます。取れなければ、ここで止まり、動いている Lista は変わりません

   ```bash
   docker compose pull webapp
   ```

3. **バックアップを取ります**（[バックアップと復元](backup.md)）。表の変更は戻せないためです
4. Lista を作り直します

   ```bash
   docker compose up -d webapp
   docker compose logs -f webapp
   ```

5. ログに `Started WebApplicationKt` が出て、画面のいちばん下（フッター）に新しい版が出ていれば済みです

- 表の変更（DB の移行）は、起動のときに自動で当たります。別に打つコマンドはありません
- 版を飛ばして上げてもかまいません。間の版の表の変更も、順に当たります
- `.env` を書き換えたのに作り直されないときは、`docker compose up -d --force-recreate webapp` を打ちます

## 前の版に戻す

**表の変更は戻りません。** 新しい版が表を変えたあとに、前の版をそのまま起動すると、動かないことがあります。戻すときは、手順 3 で取ったバックアップから戻します。

1. `.env` の `LISTA_VERSION` を前の版に戻します
2. [バックアップと復元](backup.md) の「復元する」の手順で、版上げの前の dump を戻します
3. Lista を起動します

版上げのあとに入れたデータは、この手順で消えます。

## 設定を変えるときの注意

- `LISTA_FLYWAY_IGNORE_MIGRATION_PATTERNS` は既定（`*:future`）のままにしてください。`*:missing` などを足すと、DB と版の食い違いを起動のときに見つけられなくなります
