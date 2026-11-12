# CLAUDE.md

Lista の配布用のリポジトリ（public）。**ソースコードは置かない。** ソースと、イメージを作るワークフローは private の `propagandist/lista-internal` にある（経緯は propagandist/lista-internal#116）。

## このリポジトリに置くもの

| ファイル | 正本 |
|---|---|
| `docs/manual/`・直下の `compose.yaml`・`compose.ipmsg.yaml`・`lista.env.example` | `lista-internal` の `docs/manual/`（同じ版のタグから写す。ここで直さない） |
| `README.md`・`LICENSE`・`THIRD-PARTY-NOTICES.md`・`docs/release-check.md`・`icon.png` | このリポジトリ |

- 版を出すときは [docs/release-check.md](docs/release-check.md) を上から順に通す。イメージ（`ghcr.io/propagandist/lista`）は、確かめてから公開する
- `LICENSE` は作業者が確かめた文面（2026-11-11）。変えるときは作業者に確かめる
- README の「外へ送るもの」は、`lista-internal` の `docs/manual/admin/data-flows.md` と食い違わないようにする（org の `legal-baseline.md` §2.2）
- 配っているイメージは、当社の設備が利用者のデータを受け取らない（法務の区分 4）

## CI / ワークフロー
GitHub Actions の無料枠は **propagandist org 全体で共有**。枯らすと全リポジトリの CI と
デプロイが止まる。**ワークフローを増やす・トリガーを変える前に** propagandist の判断規約を読むこと:
`gh api repos/propagandist/.github/contents/docs/ci-strategy.md --jq .content | base64 -d`
**作業の型**（プランを起票で止める／起票の作法／着手前／本文／マージ／**エージェントの設定**）は同
`docs/work-conventions.md`（**軸が違う。CI の有無と関係なく効く**）。
**文章の基準**（一貫性・明確さ・リズム・文体・正確さ・網羅性）は同 `docs/writing-baseline.md`
（**同じく軸が違う。人に読ませる文章を書き終えたら読む**）。
**表側**（README の型・description・topics・既定ブランチ・Releases）は同 `docs/repo-surface-baseline.md`
（**これらを触る前・版を切る前・public にする前に読む**）。
**値**（既定ブランチ・ブランチ名・コミット規約・ラベル・merge 方式・表記の対・定番語彙）は
**このリポジトリが持つ**——下の `## 作業の型` と `## 文章の値` に書く。
**表側の値は逆で、正本が持つ**（揃っていること自体が目的のため）。
ただし上の値と、Release 名の前半（版の表記）はこのリポジトリが持つ。
**正本が実態と合わない／どこにも当てはまらないと気づいたら**、ここへ例外を書き足さない。
リポジトリが変わっても同じなら `tinyrules/.github`（配り元から受け取ったものなら、その配り元）へ起票し、
変わるなら値としてここに書く（分かれ目は同 `docs/work-conventions.md` §6）。

- このリポジトリにワークフローは置かない（イメージは `lista-internal` の Release ワークフローが作る）
- ソースを持たない配布用の public リポジトリは、`repo-surface-baseline.md` §1 の前提（OSS のブランディング）に当てはまらない（tinyrules/.github#102）。決まるまで、README の §3.6 の 6 は「配布物である旨」と読み替える

## 作業の型

- 既定ブランチは `main`。ブランチは `feature/<短い名前>`
- コミットは Conventional Commits の `<type>(<scope>): <subject>`。**subject は日本語・常体**。type は `docs` / `chore`
- トレーラーは `Co-Authored-By: <そのセッションのモデル名> <noreply@anthropic.com>`
- **merge は squash だけ**。`--subject` は触らない
- ラベルは GitHub の既定の 9 種。足さない
- issue は `lista-internal` に立てる（このリポジトリの Issue は、利用者からの受け口）
- 版は数字だけで書く（`0.9.2`）。タグ名・Release 名の前半を `lista-internal` の版と同じにする

## 文章の値

- **README・docs は日本語**。README は敬体、`CLAUDE.md` と `docs/release-check.md` は常体（`docs/manual/` は `lista-internal` の値に従い敬体）
- 句点は「。」、読点は「、」
- 和文と英数字の間に半角スペースを入れる
- 長音は付ける（「ユーザー」「サーバー」）
