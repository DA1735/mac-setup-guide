# 04. 必須: TeX (LaTeX) 執筆環境の構築

数学科の学生にとって、TeX環境の構築は**必須**です。
本マニュアルでは基本的な本体インストールを行った後、執筆用エディタを **1. 標準のTeXShopを利用する** または **2. VS Codeを利用する** のいずれかから環境を選んで構築する手順を説明します。

## MacTeX 本体のインストール
ターミナルを開き、Homebrewを使ってフルインストールを行います。（数十分かかる場合があります）
```bash
brew install --cask mactex
```
> ※ パスワードを聞かれたら、Macにログインする際のパスワードを入力します。（文字は画面に表示されません）

インストール後、用紙サイズをA4に設定し、パッケージ群を最新化します。
```bash
sudo tlmgr paper a4
sudo tlmgr update --self --all
```

---

これ以降は、ご自身の好みに合わせて環境を選択してください。

## 1. 標準の「TeXShop」を使う
MacTeXをインストールすると、標準で付属してくる専用のGUIエディタ「TeXShop」を使う方法です。最もシンプルで、多くの数学者が愛用している伝統的な環境です。

1. **起動する**
   Macの「アプリケーション」フォルダ内にある `TeX` フォルダの `TeXShop.app` を起動します。
2. **タイプセット（コンパイル）**
   ソースコードを入力し、左上の「タイプセット」ボタンをクリックするだけでPDFが生成されます。
   ※ エンジンの変更など詳細な設定が必要になった場合は、TeXShopの「環境設定」から行います。

---

## 2. 「VS Code」を使う
プログラミング等でも使える汎用エディタ「VS Code」を最強のLaTeX執筆環境に仕立てる方法です。

### VS Code 本体のインストールと拡張機能
```bash
brew install --cask visual-studio-code
```
1. 起動後、左側の拡張機能アイコン（ブロックのようなマーク）を開きます。
2. 検索窓に `Japanese` と入力し、`Japanese Language Pack for Visual Studio Code` をインストールして再起動します。
3. 同様に `LaTeX Workshop` という拡張機能を検索してインストールします。

VS Code を利用する場合、以下の **「2-1. 通常の利用」** か **「2-2. 自動コンパイルの利用」** のどちらかを選んでください。

### 2-1. 通常の使い方（手動コンパイル）
VS Codeの標準設定のまま、コンパイルを手動で実行するシンプルな使い方です。

1. 拡張機能「LaTeX Workshop」を設定した状態で `.tex` ファイルを作成します。
2. ファイルの先頭に、以下の一行（マジックコメント）を記述することで、コンパイルに使用するエンジンを毎回明示します（ここでは標準のuplatexを指定）。
   ```latex
   %!TEX program = uplatex
   ```
3. エディタ左側の「TeX」アイコンをクリックし、**「Build LaTeX project」**内の項目をクリックして手動で変換処理を実行します。
   ※ 生成されたPDFを確認する際は、エディタ右上の虫眼鏡アイコンをクリックします。

### 2-2. 自動コンパイルの使い方（推奨）
ファイルを保存するたびに裏側で変換処理（latexmk）を走らせ、PDFを自動更新させる最も効率的な設定です。

#### ① コンパイル設定ファイル `.latexmkrc` の作成
1. VS Codeで「新しいテキストファイル」を開き、以下の内容をペーストします。
```perl
#!/usr/bin/perl
$latex            = 'uplatex -synctex=1 -interaction=nonstopmode -file-line-error %O %S';
$lualatex         = 'lualatex -synctex=1 -interaction=nonstopmode -file-line-error %O %S';
$xelatex          = 'xelatex -synctex=1 -interaction=nonstopmode -file-line-error %O %S';
$bibtex           = 'upbibtex %O %B';
$biber            = 'biber %O --bblencoding=utf8 -u -U --output_safechars %B';
$makeindex        = 'upmendex %O -o %D %S';
$dvipdf           = 'dvipdfmx %O -o %D %S';
```
2. 保存場所を **ホームディレクトリ (`/Users/あなたの名前/`)**、ファイル名を **`.latexmkrc`**（先頭のドットを忘れないように）として保存します。

#### ② VS Code 設定ファイル `settings.json` の編集
1. VS Code 上で `Command + ,` を押し、設定画面を開きます。
2. 画面右上の紙のアイコン（「設定(JSON)を開く」）をクリックします。
3. 波括弧 `{ }` の中に以下の内容をコピー＆ペーストします（他に設定が書かれている場合は末尾に `,` カンマを追加してからペーストしてください）。

```json
    "files.autoSave": "afterDelay",
    "latex-workshop.latex.autoBuild.run": "onFileChange",
    "latex-workshop.view.pdf.viewer": "tab",
    "latex-workshop.latex.recipes": [
        {
            "name": "upLaTeX",
            "tools": ["latexmk_uplatex"]
        },
        {
            "name": "LuaLaTeX",
            "tools": ["latexmk_lualatex"]
        }
    ],
    "latex-workshop.latex.tools": [
        {
            "name": "latexmk_lualatex",
            "command": "latexmk",
            "args": ["-gg", "-pdflua", "-outdir=%OUTDIR%", "%DOC%"]
        },
        {
            "name": "latexmk_uplatex",
            "command": "latexmk",
            "args": ["-gg", "-pdfdvi", "-outdir=%OUTDIR%", "%DOC%"]
        }
    ],
    "latex-workshop.latex.clean.fileTypes": [
        "*.aux", "*.bbl", "*.blg", "*.idx", "*.ind", "*.lof", "*.lot", "*.out", "*.toc", "*.log", "*.fdb_latexmk", "*.dvi", "*.synctex.gz"
    ]
```

これ以降、文書を入力して数秒待つと自動保存され、右側のPDFタブがリアルタイムで更新・表示されるようになります。

---
エンジンの違いやトラブルシューティングなど、より専門的な知識を得たい場合は、参考資料の「完全ガイド」を参照してください。
