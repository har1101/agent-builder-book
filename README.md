# Re:VIEW Book Workspace

Docker で Re:VIEW 本をビルドするためのリポジトリです。  
この README は「GitHub から clone してすぐ再現する」ための手順をまとめています。

## ディレクトリ構成

```text
.
├── README.md
└── samplebook/
    ├── compose.yml
    ├── config.yml
    ├── catalog.yml
    ├── samplebook.re
    ├── sty/
    └── ...
```

## 前提条件

- Docker Desktop が起動していること
- `docker compose` が使えること

確認コマンド:

```bash
docker version
docker compose version
```

## クローンして初回ビルド

```bash
git clone https://github.com/ryuki-imachi/review-book.git
cd review-book/samplebook
docker compose run --rm review rake pdf
```

初回は `vvakame/review:5.9` イメージを pull するため時間がかかります。  
成功すると `samplebook/book.pdf` が生成されます。

## 日常のビルドコマンド

`samplebook/` ディレクトリで実行してください。

```bash
# PDF
docker compose run --rm review rake pdf

# EPUB
docker compose run --rm review rake epub

# テキスト
docker compose run --rm review rake text

# Web
docker compose run --rm review rake web
```

## 主な編集箇所

- 書名: `samplebook/config.yml:25` の `booktitle`
- 出力ファイル名: `samplebook/config.yml:19` の `bookname`
- 著者名: `samplebook/config.yml:29` の `aut`
- 本文: `samplebook/samplebook.re`
- 章構成: `samplebook/catalog.yml`

## 新しい本ディレクトリを追加する

このリポジトリ配下で、`samplebook` とは別に新しい本を作れます。

例: `mysecondbook` を追加する場合

```bash
cd review-book

# 1) Re:VIEW プロジェクトを初期化（<repo>/mysecondbook が作成される）
docker run --rm -v "$PWD":/work -w /work \
  vvakame/review:5.9 review-init mysecondbook

# 2) compose.yml を用意（samplebook のものを流用）
cp samplebook/compose.yml mysecondbook/compose.yml

# 3) .gitignore も流用（生成物のコミット防止）
cp samplebook/.gitignore mysecondbook/.gitignore

# 4) ビルド確認
cd mysecondbook
docker compose run --rm review rake pdf
```

ポイント:

- `review-init <name>` で `<repo>/<name>` 配下に一式が生成される
- 複数本を並行管理する場合も、各本ディレクトリで同じコマンドを使える

## レイアウト補足（ヘッダー衝突対策）

A5 レイアウトで柱（ヘッダー）が重なる問題に対して、以下を調整済みです。

- `samplebook/sty/review-custom.sty`
- 偶数ページは章名、奇数ページは節名のみ表示

## 採用バージョン

- Re:VIEW 実行環境: `vvakame/review:5.9`
- 実行方式: Docker Compose

## 参考サイト

- Re:VIEW 公式リポジトリ: https://github.com/kmuto/review
- Re:VIEW クイックスタート: https://github.com/kmuto/review/blob/master/doc/quickstart.md
- Docker イメージ（Re:VIEW）: https://github.com/vvakame/docker-review
- Docker Hub（vvakame/review）: https://hub.docker.com/r/vvakame/review
- TechBooster ReVIEW-Template: https://github.com/TechBooster/ReVIEW-Template
