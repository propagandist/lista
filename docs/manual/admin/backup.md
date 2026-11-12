# バックアップと復元

Lista のデータは、PostgreSQL の 1 つのデータベースにすべて入っています。タスク・テンプレート・利用者・添付したファイル・通知の履歴・ログインのセッションも含みます。**バックアップするのはこの DB だけです。** Lista のコンテナは何も持たないので、作り直してかまいません。

## バックアップを取る

compose のフォルダーで、次を実行します。`backup/` に、日時の入った名前で書き出します。

```bash
mkdir -p backup
docker compose exec -T db pg_dump -U lista -d lista -Fc > backup/lista-$(date +%Y%m%d-%H%M%S).dump
```

- 動かしたままで取れます
- `cron` などで毎日動かし、ホストの外（別の機械やバックアップの仕組み）へも持ち出してください
- 古いものを消すときの例: `find backup -name 'lista-*.dump' -mtime +14 -delete`（14 日より古いもの）
- ファイルには業務のデータがすべて入っています。ほかの人が読めない場所に置いてください

## 復元する

1. Lista だけを止めます（DB は動かしたまま）

   ```bash
   docker compose stop webapp
   ```

2. dump を流し込みます。今の DB の中身は置き換わります

   ```bash
   docker compose exec -T db pg_restore -U lista -d lista --clean --if-exists < backup/lista-<日時>.dump
   ```

3. Lista を起動し、ログに `Started WebApplicationKt` が出ることを確かめます

   ```bash
   docker compose start webapp
   docker compose logs -f webapp
   ```

- dump を取ったときより新しい版の Lista で起動すると、足りない表の変更が起動のときに当たります。逆に、dump より古い版の Lista では、起動しても正しく動かないことがあります（[版上げ](upgrade.md)）

## ntfy のデータ

同梱の ntfy のデータ（ボリューム `ntfy`）はバックアップしなくてもかまいません。中にあるのは、送信用のユーザーとトークン（[通知の設定](notifications.md) の手順で作り直せます）と、24 時間で消える通知の控えだけです。利用者ごとのトピックは Lista の DB にあります。
