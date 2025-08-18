## KiCad ドキュメントを Asciidoctor → DocBook(XML) → Pandoc → Markdown(GFM) に変換する手順

以下では Debian/Ubuntu 系 Linux 環境を想定しています。macOS などではパッケージ導入コマンドが異なる場合があります。

---

### 1. リポジトリの取得

```bash
git clone https://gitlab.com/kicad/services/kicad-doc.git
cd kicad-doc
```

### 2. 必要なツールのインストール

1. **Ruby / Asciidoctor**
    ```bash
    sudo apt update
    sudo apt install -y ruby ruby-dev build-essential
    sudo gem install asciidoctor
    ```
    Bundler を利用する場合は `gem install bundler` 後に `bundle install --path vendor/bundle` を実行してください。

2. **Pandoc**
    ```bash
    sudo apt install -y pandoc
    ```
    バージョンが古い場合は Pandoc 公式サイトからバイナリを取得して PATH に追加します。

---

### 3. 単一ファイルを変換する基本コマンド

```bash
# Asciidoctor で DocBook(XML) を生成
asciidoctor -b docbook5 src/pcbnew/pcbnew.adoc -o build/docbook/pcbnew.xml

# Pandoc で Markdown(GFM) へ変換
pandoc build/docbook/pcbnew.xml -f docbook -t gfm -o build/markdown/pcbnew.md
```

`src/pcbnew/pcbnew.adoc` は変換対象、`build/docbook/` と `build/markdown/` は出力先です。存在しない場合は `mkdir -p` で作成します。

---

### 4. 全ドキュメントを一括変換するスクリプト例

```bash
mkdir -p build/docbook build/markdown

find src -name '*.adoc' -print0 |
while IFS= read -r -d '' adoc; do
    rel=${adoc#src/}
    base=${rel%.adoc}
    mkdir -p "build/docbook/$(dirname "$base")"
    mkdir -p "build/markdown/$(dirname "$base")"

    asciidoctor -b docbook5 "$adoc" -o "build/docbook/$base.xml"
    pandoc "build/docbook/$base.xml" -f docbook -t gfm -o "build/markdown/$base.md"
done
```

`asciidoctor` に属性を追加したい場合は `-a` オプションを利用し、Pandoc では `--wrap=none` などで行折り返しを調整できます。

---

### 5. 追加の調整例

| 課題/要望 | 対処案 |
|-----------|--------|
| 画像・図表のパスが解決されない | `asciidoctor -a imagesdir=<パス>` や `pandoc --resource-path=<パス>` を指定 |
| テーブル/数式の書式が崩れる | Pandoc オプション `--tables` `--mathjax` を必要に応じて追加 |
| Markdown のスタイルを調整したい | `--standalone` `--toc` など好みのオプションを付与 |

---

### 6. 生成物の確認と後処理

1. `build/markdown` に Markdown ファイルが生成されていることを確認します。
2. DocBook(XML) が不要であれば `rm -r build/docbook` で削除します。
3. 生成物をバージョン管理しない場合は `.gitignore` に `build/` を追加します。

