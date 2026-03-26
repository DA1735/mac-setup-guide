# 02. Homebrew の導入と基本操作

Mac における「パッケージ管理システム」である **Homebrew** を導入します。これにより、コマンド操作で簡単にアプリをインストール・更新できるようになります。

## ターミナルの起動
1. `Command + Space` を押して Spotlight 検索を開き、「Terminal」または「ターミナル」と入力して起動します。

## Homebrew のインストール
ターミナルに以下のコマンドをコピー＆ペーストして Enter を押してください。
※ パスワードを求められたら、Mac のログインパスワードを入力します（入力中の文字は画面に表示されませんが、入力はされています）。

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### Path (パス) の追加
インストール完了後、画面に出力される指示に従って、`eval "$(/opt/homebrew/bin/brew shellenv)"` のようなコマンドを数回実行する必要があります。これを行わないと `brew` コマンドが認識されません。

## Homebrew の基本コマンド
今後は以下のコマンドを多用します。

- **インストール**: `brew install [パッケージ名]`
- **GUIアプリ (Cask) のインストール**: `brew install --cask [アプリ名]`
- **更新情報の確認**: `brew update`
- **インストール済みアプリの更新**: `brew upgrade`
- **インストール済みリストの表示**: `brew list`

まずは、今後の作業を楽にするために Homebrew 自体が正しく動くか確認しましょう：
```bash
brew -v
```
バージョンが表示されれば成功です。
