# dotfiles

Mac を新規購入・OS クリーンインストールした時にやるやつ

## リポジトリを clone する

ユーザーのホームディレクトリに clone する

```
$ cd ~/
$ git clone https://github.com/ryonakae/dotfiles.git
```

## Xcode と Command Line Tools for Xcode のインストール

git コマンド使うのと、Homebrew のインストールに必要

- App Store から Xcode をインストールする
- `$ xcode-select --install`を実行

## config.fish ファイルのコピー

- config.fish.example を config.fish にリネーム
  - `$ cp config.fish.example config.fish`
- PC によって設定したいものは適宜コメント or コメントアウト解除

## シェルスクリプトの実行

`dotfile`ディレクトリに移動し、シェルスクリプトを実行

```
$ cd dotfiles
$ sh symlink.sh
```

各設定ファイルのシンボリックリンクをホームディレクトリに貼る

## デフォルトの Shell を fish にする

参考：[ログインシェルを fish にしてみる \- Qiita](https://qiita.com/bleru/items/047a4e8ea2afb654d9e1)

```
# /etc/shells の末尾にfishを追記
$ sudo sh -c 'echo $(which fish) >> /etc/shells'

# ユーザのデフォルトシェルを変更
$ which fish
$ chsh -s <which fishで表示されたパス>
```

Shell を再起動で fish がデフォルトになる

### fish がおかしい場合

- `~/.config/fish/config.fish` がちゃんとエイリアスになっているか確認する
- なっていない場合 (ファイルが実在する場合) は、一度ファイルを消して、`$ cd ~/dotfiles && sh symlink.sh` を実行

## fish の設定

### fishfile に書かれたパッケージをインストール

```
$ fisher update
```

### HOMEBREW_GITHUB_API_TOKEN の設定

- https://github.com/settings/tokens にアクセスして、Homebrew 用のトークンを作成 (既にあれば Regenerate)
- トークンを config.fish の HOMEBREW_GITHUB_API_TOKEN の箇所にコピペ

## Vim の設定

```
$ mkdir -p ~/.vim/bundle
$ git clone https://github.com/Shougo/neobundle.vim ~/.vim/bundle/neobundle.vim
$ git clone https://github.com/Shougo/vimproc ~/.vim/bundle/vimproc
$ cd ~/.vim/bundle/vimproc
$ make -f make_mac.mak
```

ここまでやると NeoBundle と VimProc がインストールされる

```
$ vim hoge
:NeoBundleInstall
!q
```

とすると Vim のプラグインがインストールされる

## Homebrew のインストール

[Homebrew](https://brew.sh/ja/)のインストール

```
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

※ちょいちょいインストール用のコマンドが変わるのでサイトに行ってコピペ推奨

## brew bundle の実行

1. Brewfile.example を Brewfile にリネーム

- `$ cp Brewfile.example Brewfile`

2. PC によってインストールしたいものを適宜コメントアウト解除
3. `$ brew bundle`を実行

Brewfile に記述した処理が実行される

## Homebrew でインストールしたやつを優先的に利用する

`/etc/paths`の順番を入れ替える

```
/usr/local/bin
/usr/bin
/bin
/usr/sbin
/sbin
```

`$ exec $SHELL`で反映される

## Ruby, Node, Python とか設定する

Homebrew 経由でインストールした anyenv を使う

[オールインワンな開発環境を anyenv で構築する](https://zenn.dev/ryuu/articles/use-anyversions)

PHP は phpenv ではなく Homebrew を使うのが良い

## direnv の設定する

ディレクトリごとに環境変数の設定ができて便利  
例えば、特定ディレクトリ以下で GitHub アカウントを会社のものに切り替えたりできる

- [direnv を使おう - Qiita](http://qiita.com/kompiro/items/5fc46089247a56243a62)
- [direnv を使って複数の git コミッタ名を切り替える - MANA-DOT](http://blog.manaten.net/entry/direnv_git_account)

### 使い方

```
# 環境変数切り替えたいディレクトリに移動
$ cd ~/Projects/Private

# .envrcの作成、編集(Vimが起動する)
$ direnv edit .
```

`.envrc`に以下のように書く

```
export GIT_COMMITTER_NAME="YOUR NAME"
export GIT_COMMITTER_EMAIL="mail@example.com"
export GIT_AUTHOR_NAME="YOUR NAME"
export GIT_AUTHOR_EMAIL="mail@example.com"
```

## プログラミング用フォント

- 英語フォント
  - [SauceCodePro Nerd Font](https://github.com/ryanoasis/nerd-fonts/tree/master/patched-fonts/SourceCodePro)
  - 「Sauce Code Pro Nerd Font Complete」を使う
- 日本語フォント
  - [Source Han Code JP](https://github.com/adobe-fonts/source-han-code-jp)

ちなみにフォントは RightFont + Dropbox で管理したい

- Rightfont を起動し、Open Library -> Dropbox に保存してるライブラリを選択
- Sauce Code Pro Nerd Font, Source Han Code JP を有効化

## 各 App の設定

- 当たり前だけど各 App の設定は自分でやる
- Dropbox/App にアプリの設定を保存したりしている

## その他

### Dock の表示/非表示を早くする

```
$ defaults write com.apple.dock autohide-time-modifier -float 0.15;killall Dock
$ defaults write com.apple.dock autohide-delay -float 0;killall Dock
```

### SSH 鍵を新しい PC に丸ごと移行する

[SSH 鍵を新しい PC に移動させる方法（mac） \#SSH \- Qiita](https://qiita.com/yamaking/items/65da45bd69e616f8f88d)

### Homebrew で使われていないものを検索

[Homebrew で使われていない formula を削除する :: by and for engineers](https://yulii.github.io/brew-cleanup-installed-formulae-20200509.html)

```
$ brew list --formula | xargs -I{} sh -c 'brew uses --installed {} | wc -l | xargs printf "%20s is used by %2d formulae.\n" {}' | grep '0 formula'
```

### PHP を Homebrew でインストール・切り替えする

[Mac で複数のバージョンの PHP を同時に使う \#PHP \- Qiita](https://qiita.com/koriym/items/17662cd9c44c43081bf9)

### SourceTree で push できないとき

[SourceTree で GitHub の Personal access tokens を利用する方法](https://zenn.dev/koushikagawa/articles/3c35e503c8553a)

## 日々のメンテナンス

[【Homebrew】コマンド一覧 \#homebrew \- Qiita](https://qiita.com/P-man_Brown/items/82b7e2f1e108a72d89f4)

```
# Homebrew自体を更新
$ brew update

# 更新可能なformula & caskを表示
$ brew outdated

# 他のformulaの依存関係としてのみインストールされ不要となったものをアンインストール
$ brew autoremove

# formulaやcaskのキャッシュを削除
$ brew cleanup

# システムに問題がないかチェック
$ brew doctor
```

## 参考

- [pixyzehn/dotfiles](https://github.com/pixyzehn/dotfiles)
