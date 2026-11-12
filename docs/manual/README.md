# Lista の使い方

Lista は、毎月決まって行う仕事（定例）と、その都度入ってくる仕事（突発）を、1 つの一覧で管理する Web アプリです。期限が近いものを目立たせ、朝のまとめと担当の知らせを IP メッセンジャーや ntfy で届けます。

## 利用者ガイド

Lista を使う人のためのガイドです。

1. [Lista のあらまし](user/overview.md)
2. [ホーム](user/home.md)
3. [タスクの操作](user/tasks.md)
4. [突発タスク](user/adhoc.md)
5. [月の記録](user/sheets.md)
6. [カレンダー](user/calendar.md)
7. [テンプレート](user/templates.md)
8. [通知の受け取り方](user/notifications.md)
9. [利用者の管理](user/accounts.md)
10. [困ったとき](user/faq.md)

## 導入・運用ガイド（管理者向け）

Lista を自分の組織で動かす人のためのガイドです。

1. [構成と必要なもの](admin/requirements.md)
2. [最短の起動](admin/quickstart.md)
3. [ログインの設定](admin/oidc.md)
4. [リバースプロキシと https](admin/reverse-proxy.md)
5. [通知の設定](admin/notifications.md)
6. [Outlook アドイン](admin/outlook-addin.md)
7. [AI の下書き](admin/ai.md)
8. [データの送り先](admin/data-flows.md)
9. [バックアップと復元](admin/backup.md)
10. [版上げ](admin/upgrade.md)
11. [設定の一覧](admin/configuration.md)
12. [セキュリティ上の注意](admin/security.md)
13. [困ったとき（管理者向け）](admin/troubleshooting.md)

同梱のファイル:

- [compose.yaml](files/compose.yaml): Lista と PostgreSQL（と、任意で ntfy）を動かす Docker Compose のファイル
- [compose.ipmsg.yaml](files/compose.ipmsg.yaml): IP メッセンジャーの在席を使うときに重ねるファイル
- [lista.env.example](files/lista.env.example): `.env` のひな形

このガイドは、Lista の画面の「使い方」からも開けます。
