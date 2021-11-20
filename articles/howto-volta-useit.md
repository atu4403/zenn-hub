---
title: "nodejs気軽にバージョンアップできない説"
emoji: "📑"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['Nodejs']
published: true
---

普段動かしているnodejsアプリが突然エラー吐いて困ることありませんか？

私の場合、原因はnodeをhomebrewでインストールしていたこと。`brew upgrade`とかしたらnodeもアップデートされてしまうので、いつの間にかv17が入ってた。stableはv16なのに。

![](/images/howto-volta-useit/2021-11-19-20-14-12.png)

ついでに言うと、[nodebrew](https://github.com/hokaccha/nodebrew)使ってたのにこれもhomebrewの方で上書きされてたみたい。いや、nodebrewは悪くないんだけど。

## 問題点

- 自作cliアプリが、nodejsのバージョンアップにより使えなくなる可能性がある
- homebrewは日頃使わないので、知らずにバージョンアップさせてしまう
- 他のnode管理ツールを使って自在にバージョン切り替えはできても、システム全体が同じバージョンになる
- globalインストールしたアプリケーションはインストールしたバージョンに切り替えないと使えない

そこでググっていたらvoltaというnodejsの管理ツールを見付けた。

https://volta.sh/

## voltaの特徴

- 速い
- 複数バージョンのnodejsを使い分けられる
- プロジェクトで使用しているnodeバージョンを固定できる
- globalインストールしたアプリケーションは、そのバージョンのnodejsで動く

## install前準備

現在のnodeをアンインストールする。

homebrew

```bash
brew uninstall node
```

nodebrew

`~/.nodebrew`以下を削除


## install

Unixならワンライナー

```bash
curl https://get.volta.sh | bash
```

macならhomebrewで

```bash
brew install volta
```

windowsならインストーラーがあるようなので公式からどうぞ。

https://docs.volta.sh/guide/getting-started

---

`volta -v`でバージョン番号が出たらインストール終了

```bash
❯ volta -v
1.0.5
```
## setup

homebrewで入れた場合はパスが通ってないので、通してくれるコマンドがある。

```bash
volta setup
```

これで`bashrc`や`zshrc`に以下が追記される。

```bash
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"
```

ちなみに、`volta setup`を複数回行っても追記が重複することはなさそう。

## 使い方

`volta install node`でstableバージョンのnodeがインストールされる。

```bash
# stable
volta install node
# バージョン指定
volta install node@15
# yarnもOK
volta install yarn
```

バージョンの切り替えも`volta install`で行う。インストール済のバージョンならダウンロードは行われず切り替えられる。

![ss](/images/howto-volta-useit/2021-11-19-20-38-41.png)

> ちなみに`~ via...`の行はプロンプトです。staship使ってます。
> https://starship.rs/
### プロジェクトでバージョンを固定する

`package.json`のあるディレクトリでnodeのバージョンを固定したい時はpinを打つ。

```bash
> volta pin node@16
success: pinned node@16.13.0 (with npm@8.1.0) in package.json
```

このコマンドにより、`package.json`に以下の内容が書き込まれる。

```bash
  "volta": {
    "node": "16.13.0"
  }
```

そうなるとバージョンの切り替えはできなくなる。

```bash
> volta install node@15
success: installed and set node@15.14.0 as default
   note: you are using node@16.13.0 in the current project
> node -v
v16.13.0
```

### globalインストール

`npm install -g`としてグローバルにインストールしたとします。

nodejsをhomebrewでインストールした場合、他の管理ツール(nodebrew、nvm、n、nvm-windows、nave、nodist等)でインストールした場合で色々と挙動が変わってしまう。

v8でしか動かないものをv16で動かそうとしたり、バージョンを切り替えたら再インストールする必要があったりで面倒。

voltaではv16でグローバルインストールしたコマンドを、v15に切り替えても動作します。

![ss](/images/howto-volta-useit/2021-11-19-20-51-51.png)

上の画像ではnode@15でインストールした`speed-test`がnode@16に切り替えても問題なく動いている様子がわかる。

`volta list all`でグローバルインストールした一覧と動作環境が確認できる。

```bash
> volta list all
⚡️ User toolchain:

    Node runtimes:
        v15.14.0 (default)
        v16.13.0

    Package managers:
        npm:
            v8.1.4 (default)
        Yarn:
            v1.22.17 (default)

    Packages:
        pm2@5.1.2 (default)
            binary tools: pm2, pm2-dev, pm2-docker, pm2-runtime
            platform:
                runtime: node@16.13.0
                package manager: npm@built-in
        speed-test@2.1.0 (default)
            binary tools: speed-test
            platform:
                runtime: node@15.14.0
                package manager: npm@built-in
```

## volta使い方まとめ

オプション一覧
```
SUBCOMMANDS:
    fetch          Fetches a tool to the local machine
    install        Installs a tool in your toolchain
    uninstall      Uninstalls a tool from your toolchain
    pin            Pins your project's runtime or package manager
    list           Displays the current toolchain
    completions    Generates Volta completions
    which          Locates the actual binary that will be called by Volta
    setup          Enables Volta for the current user / shell
    run            Run a command with custom Node, npm, and/or Yarn versions
    help           Prints this message or the help of the given subcommand(s)
```

以下、使いそうな機能
### fetch
ダウンロードのみを行う

```bash
volta fetch node@12
```

### install
インストール、バージョンの切り替え

```bash
volta install node@16
```

### pin
プロジェクトのnodeバージョン固定

```bash
volta pin node@16
```

### which
本体の場所確認

```bash
# 普通のwhich
> which node
/Users/foo/.volta/bin/node
# 本体の場所
> volta which node
/Users/foo/.volta/tools/image/node/16.13.0/bin/node
```

その他、必要になったら追加していきます。
