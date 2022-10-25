---
title: "fireとpoetryで作る自分用cliアプリケーション(2022)"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['python','fire']
published: true
---

定型作業や自作ツールを使うときにコマンド1発で使用できると便利です。

例えば、URLにアクセスしてデータをダウンロードしDBにぶち込むなどなど。

以前にこのような記事を投稿したことがあるのですが欠点がありました。

https://zenn.dev/atu4403/articles/b776a9f2b7a516d195d1

公開できるCLIアプリケーションという前提でしたので、汎用的に使える一方で細かな修正が面倒でした。

なので今回は「自分が使えればOK」という観点で書き直してみたいと思います。

## プロジェクト作成

例として`myapp`というコマンドを作成します。myappの部分は好きに命名してください。

```bash
poetry new myapp
cd myapp
poetry add fire
```

myappディレクトリにcli.pyというファイルを作成します。poetryの設定によりsrcディレクトリが無い場合もあります。

```bash
├── src
│   └── myapp
│       ├── __init__.py
│       └── cli.py
└── tests
    └── __init__.py
```

## コマンドの作成

```python
import fire


class Calculator(object):
    def __init__(self, detail=False) -> None:
        self.detail = detail

    def add(self, x, y, offset=0):
        x, y = x + offset, y + offset
        if self.detail:
            return f"{x} + {y} = {x+y}"
        return x + y

    def mul(self, x, y):
        if self.detail:
            return f"{x} * {y} = {x*y}"
        return x * y


if __name__ == "__main__":
    fire.Fire(Calculator)

```

`poetry shell`で仮想環境に入り、cli.pyを実行するとコマンドライン引数を受け取って関数が走ります。

```bash
# examples
> python src/myapp/cli.py add 2 3
5
> python src/myapp/cli.py add 2 3 --detail --offset=1
3 + 5 = 8
```

この場合、`--detail`は全ての関数に付けられるオプション、`--offset`はadd関数のみに付けられるオプションになります。

## コマンドを使いやすくする

前回は、どこからでも使用できるように「ビルドしてからインストール」という方法を解説しました。
しかしこれは、コマンドを修正する度にやり直す必要があるので、自分で使うには不便です。

今回は「shell関数にする」という方法で行います。fishの例ですが他のshellでも概念は同じです。

```bash
function mytools
    pythonインタプリタのフルパス cli.pyのフルパス $argv
end
```

`mytools`というコマンドで使用すると仮定します。`pythonインタプリタのフルパス`は`which python`で取得できます。(仮想環境の場合は`poetry shell`等でアクティベートすることを忘れないでください)

## ドキュメントの作成

fireは`--help`オプションが自動で作成されるのですが微妙に読みにくいです。なので`README.md`などにわかりやすいドキュメントを作成します。
これをブラウザで開くコマンドを作成します。

```python
def doc(self):
    from pathlib import Path
    readme = Path(__file__).parent / "../README.md"
    subprocess.Popen(
        ["open", "-a", "/Applications/Google Chrome.app", readme.resolve()]
    )
```

この場合は`/Applications/Google Chrome.app`で開きます。必要に応じて変更してください。

実行するために以下の設定が必要になります。

### ブラウザ(chrome系)に機能拡張を追加

chrome系だと[Markdown Preview Plus](https://chrome.google.com/webstore/detail/markdown-preview-plus/febilkbfcbhebfnokafefeacimjdckgl?hl=ja)を追加するとmarkdownを見やすい感じで開くことができます。

以上を設定すると`mytools doc`のようなコマンドで簡単にドキュメントを開くことができます。
