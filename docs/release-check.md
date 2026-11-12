# 配る前の確かめ

版を `ghcr.io/propagandist/lista` へ上げ、このリポジトリの Release を出す前に通す手順。イメージは、非公開のリポジトリ（`propagandist/lista-internal`）の Release ワークフローが、入力 `distribute` を付けたときに作る。この手順は、作ったイメージを外へ見せる前の関門になる。

**順番は「確かめる → 公開する」で、逆は無い。** 一度公開したイメージは、消しても誰かが取得している。混入したものは取り返せない。

## 0. 版を作る（lista-internal の側）

- [ ] `lista-internal` の develop の先頭に注釈つきタグを打ち、`gh workflow run release.yml --ref develop -f distribute=true` で流す。版がタグちょうど（`x.y.z`）でなければ、ワークフローが push せずに落ちる
- [ ] Summary に `ghcr.io/propagandist/lista:<版>`（linux/amd64・linux/arm64）が出ていること
- [ ] push が `denied` で落ちたら: パッケージはラベルで `propagandist/lista`（このリポジトリ）に結び付き、push するのは `lista-internal` のワークフローなので、書き込みが許されていないことがある。パッケージの設定の「Manage Actions access」に `lista-internal` を Write で足してから、失敗したジョブを流し直す（確かめていない。初めての版で確かめて、この行を直す）
- [ ] このリポジトリの `docs/manual/` と、直下の `compose.yaml`・`compose.ipmsg.yaml`・`lista.env.example` を、`lista-internal` の同じタグの `docs/manual/` から写し直す（`git archive <版> docs/manual`）

## 1. イメージに社内のデータが入っていないか

- [ ] 空の DB で起動し、ログインする前の利用者の表が空であること（`LISTA_INITIAL_ACCOUNT_EMAIL` を空にして起動し、`select count(*) from accounts` が 0）
- [ ] テンプレートの表が空であること（`select count(*) from task_templates` が 0）
- [ ] jar の中に、社内の取引先名・社内のホスト名・社員のメールアドレスが無いこと。`app.jar` を取り出し、中のテキストで `propagandist.co.jp`・`in.propagandist`・取引先名の一覧を探す。当たるのが、権利の表示（`PROPAGANDIST CORPORATION`）とアドインの提供者名だけであること
- [ ] 秘密が無いこと（鍵・トークン・パスワード）。走査の仕方は propagandist org の `security-verification.md` §1
- [ ] ラベルの `org.opencontainers.image.source` が `https://github.com/propagandist/lista`、`org.opencontainers.image.version` が出す版であること

## 2. 外へ送る先が README と合っているか

- [ ] 前の版から、外へ送る先（ログインのプロバイダ・Anthropic・ntfy・IP メッセンジャー・CDN）が増えていないこと。増えたら、公開する前に README「外へ送るもの」・マニュアルの「データの送り先」・画面の表示を直す
- [ ] AI へ送る項目（件名・差出人の表示名・受信日時・本文の先頭 30,000 字）が変わっていないこと。変わったら同じく直す

## 3. 第三者のソフトウェアの一覧

- [ ] イメージの `app.jar` の `BOOT-INF/lib/` を数え、`THIRD-PARTY-NOTICES.md` の Java のライブラリと合うこと。依存を足した・上げた版では作り直す
- [ ] Typst の版（ラベル `io.propagandist.lista.typst.version`）が前の版から変わっていたら、Typst の crate と埋め込みのフォントの一覧を、その版の `Cargo.lock` と `typst-assets` の NOTICE から作り直す
- [ ] イメージの `/usr/share/doc/typst/` に `LICENSE`・`NOTICE`・`typst-assets-NOTICE` があること
- [ ] GPL・LGPL・AGPL の依存が、Java のライブラリに増えていないこと（二者択一で選べるものを除く）。増えたら、公開する前に LICENSE 第 5 条で足りるかを確かめる

## 4. 手順どおりに動くか

- [ ] このリポジトリを別の場所に clone し、README「使い方」のとおりに打って、ログインのプロバイダでログインできること（`.env` の `LISTA_VERSION` を出す版にする）
- [ ] `docker buildx imagetools inspect ghcr.io/propagandist/lista:<版>` に、`linux/amd64` と `linux/arm64` の両方があること

## 5. 公開する

- [ ] このリポジトリに `main` 以外のブランチが無く、`main` にソース（`components/`・`build.gradle.kts` など）が無いこと。`git ls-remote --heads origin` と `gh api repos/propagandist/lista/git/trees/main?recursive=1 --jq '.tree[].path'` で見る。改名する前の名前（`propagandist/lista`）を remote に持つ clone から push すると、`lista-internal` のソースがここへ届く
- [ ] 初めての版だけ: パッケージ `ghcr.io/propagandist/lista` を public にする（パッケージの設定の Danger Zone）
- [ ] Release はドラフトで作り、本文を読んでから公開する。名前は `<版> — <位置づけ>`
- [ ] 公開した後に、Release が右柱に出ていることを見る（ドラフトのままだと外から見えない）
- [ ] ログインせずに `docker pull ghcr.io/propagandist/lista:<版>` が通ること

**★ 新しいパッケージへの最初の push は、manifest の push で `denied` になることがある**（2026-11-11 実測。`lista-nas` を作ったとき）。パッケージは作られ、リポジトリに結び付いていた。失敗したジョブを流し直すと通った。
