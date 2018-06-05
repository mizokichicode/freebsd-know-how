vim
===
vim のインストールと設定方法です。

インストール
---
- **pkg** コマンドでインストールします。
- 必要最低限の機能があれば良いので、**lite版** をインストールしています。

```
#> pkg install vim-lite
```

設定
---
- vim の設定は、`~/.vimrc` へ記述します。

```
set number
set tabstop=4
set shiftwidth=4
set expandtab
set smartindent
set backspace=start,eol,indent
set showmatch

syntax on
```

もし、***UTF-8*** を扱う場合には、以下の設定も記述します。

```
set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
set fileencodings=ucs-bom,euc-jp,cp932,sjis,utf-8
```

`set fileencodings=ucs-bom,euc-jp,cp932,sjis,utf-8` を記述しておくことで、これら文字コードで作成されたファイルを自動認識します。

デフォルトエディタの設定
---
- **vim** をデフォルトのエディタとして設定する場合は、ログインシェルの設定ファイル (.cshrc等) で、**EDITOR** 環境変数へ設定します。
- **vi** の代替えとして使用する場合は、ログインシェルの設定ファイルへ `alias vi vim` を記述します。
