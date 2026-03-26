# 05. 開発環境・仮想環境の構築 (任意)

計算機科学的なアプローチや数値計算、統計解析が必要な方は、以下のツールも導入してください。

## 開発・解析環境の構築戦略（重要）

数学科や情報系の学生がPythonや統計解析ソフト（R言語など）を学ぶ際、**「環境構築でつまずかない・Macを汚さない」**ことが何よりも重要です。
本マニュアルでは、**基本的には「Docker」を使ったコンテナ環境の利用を強く推奨します**。

> 🚫 **【警告】Anaconda / Miniconda について**
> ネット上の古い記事で推奨されることが多いですが、Mac全体の環境変数を破壊する（環境汚染）、非常に重い、大学での商用利用リスクがある等の理由から、本ガイドでは**新規インストールを例外なく非推奨**とします。

---

### 【推奨】選択肢1: Dockerを使った「全部入り」コンテナ環境
「ライブラリの依存関係で悩みたくない」「PythonもRもJuliaも欲しい」「論文発表で完全な再現性が必須」という場合、Mac本体に直接パッケージを入れるのではなく、**Docker** を使うのが最強の環境汚染対策であり、データサイエンスにおける現代のベストプラクティスです（複数人でのチーム開発・環境共有にも最適です）。

```bash
# Docker Desktop for Mac のインストール
brew install --cask docker
```

**【統計・データサイエンスで有名なコンテナ】**
Docker起動後、ターミナルで以下のコマンドを打つだけで、数GBの完成済み環境が立ち上がり、ブラウザから簡単にアクセスできます。
`docker run -p 8888:8888 -v "$PWD":/home/jovyan/work [以下のイメージ名]`

1. **`jupyter/scipy-notebook`**
   - Pythonでの統計解析・機械学習（Numpy, Pandas, SciPy, scikit-learn, Matplotlib等）が全てセットアップされた標準コンテナ。
2. **`jupyter/datascience-notebook`**
   - 上記のPython環境に加え、**R言語**（tidyverse等）と **Julia言語** まで全て入った、数学・統計向けの無敵コンテナ。圧倒的におすすめです。
3. **`rocker/rstudio`** (または `rocker/verse`)
   - PythonよりもR（RStudio）をガッツリ使ってモデリングを行いたい人向けの、R公式に近い大定番コンテナ。

---

### 選択肢2: 個別に直接インストールする場合（ローカル環境）
もし「Dockerを通さずに直接Macでスクリプトを動かしたい」という場合は、決してAnaconda等は使わず、以下の**言語に特化したモダンな専用ツール**を使って構築してください。

#### A. Python を個別に入れる場合（`uv`）
Anacondaの数十倍高速で、Macを汚さないRust製の次世代管理ツール **`uv`** 一択です。

```bash
brew install uv
```

**【uv の基本的な使い方（モダンな開発フロー）】**
`uv` を使えば、仮想環境の作成からパッケージ管理、スクリプトの実行までを非常にスマートに行うことができます。

1. **プロジェクトの初期化 (`uv init`)**
   作業用のフォルダを作って移動し、プロジェクトを初期化します。
   ```bash
   mkdir my_research && cd my_research
   uv init
   ```
   ※ これにより、管理ファイル（`pyproject.toml`）やサンプルの `hello.py` が自動的に作成されます。

2. **パッケージの追加 (`uv add`)**
   研究に必要なライブラリを追加します。「仮想環境(`.venv`)の作成」から「Python本体のダウンロード」まで、**すべて自動かつ圧倒的なスピード**で行われます。
   ```bash
   uv add numpy scipy pandas matplotlib
   ```

3. **スクリプトの実行 (`uv run`)**
   書いたプログラムを実行する際は、頭に `uv run` をつけるだけです。
   面倒だった `source .venv/bin/activate` のような「環境の切り替え操作」は**一切不要**で、勝手に専用環境の中で動いてくれます。
   ```bash
   uv run hello.py
   ```

4. **番外編: 一時的なツールの起動 (`uvx`)**
   「わざわざ環境を作るほどでもないが、手軽にJupyter Notebookを開きたい」という場合は、以下のコマンドで手元のMacを一切汚さずに使い捨てツールとして起動できます。
   ```bash
   uvx jupyter lab
   ```

**【VS Code × Ruff による自動整形設定（Python用）】**
本格的にPythonスクリプトによるシステム構築や研究ツールを作成する場合、超高速ツール **Ruff** を使います。
1. VS Codeの拡張機能で `Python` と `Ruff` をインストールします。
2. 作業フォルダ内に `.vscode/settings.json` を作成し、以下を記述します。
```json
{
  "[python]": {
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.codeActionsOnSave": {
      "source.fixAll": "explicit",
      "source.organizeImports": "explicit"
    }
  },
  "ruff.organizeImports": true,
  "ruff.fixAll": true
}
```
> ※ 本ガイドの `docs/templates/test_ruff.py` で、保存時に一瞬でコードが美しく整形される「動作確認テスト」ができます。

#### B. R言語 を個別に入れる場合（`rig` + `rv` + `RStudio`）
かつてはパッケージの互換性に課題がありましたが、現在最も洗練されたRの開発・管理環境は以下の組み合わせです。

1. **R本体のバージョン管理 (`rig`)**: `brew install rig`
   - システムを汚さずに複数のRバージョンを管理・切り替えできる専用のインストールマネージャです。
2. **パッケージ管理 (`rv` または `renv`)**
   - **`rv`**: Pythonの `uv` にインスパイアされて2025年に公開されたRust製の新世代マネージャ。依存解決が圧倒的に速い最新の推奨ツールです。
   - **`renv`**: Rで長年使われている定番の管理ツール。既存プロジェクトとの互換性を重視する場合に使用します。
3. **統合開発環境 (`RStudio`)**: `brew install --cask rstudio`
   - Rを使う上で必須レベルのIDE。LaTeXでの論文執筆を補助する「Quarto」のサポートも強力で、データ分析から修論・卒論作成までシームレスに行えます。

---

### 選択肢3: そもそも構築しない「Google Colab」
「自分のPCリソース（計算パワー）を使いたくない」「今すぐコードを書きたい」場合はクラウドサービスを利用しましょう。
- **[Google Colaboratory (Colab)](https://colab.research.google.com/)**: 大学アカウント等でログインすれば、無料でGPU搭載のJupyter環境が即使えます。

---

## その他の便利ツール

- JavaScript開発 (pnpm): `brew install pnpm`
- Gitの差分を見やすくする: `brew install git-delta`

---
[README に戻る](../README.md)
