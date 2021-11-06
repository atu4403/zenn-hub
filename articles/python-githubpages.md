---
title: "自作pythonパッケージのドキュメントをお手軽に作ってgithub pagesに置く方法"
emoji: "📖"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "pdoc3"]
published: true
---

pythonで自作のパッケージを書いて[PyPI](https://pypi.org/)に上げようとして「あ、README書かなきゃ」ってなりませんか？コードやテストは苦にならないけどドキュメントを作るのは面倒ですよね。

ドキュメント自動作成ツールも考えてみたけど、pydocはデザインがアレだし[Sphinx](https://www.sphinx-doc.org/ja/master/usage/installation.html)は設定が面倒。
色々ググってみた結果、[pdoc3](https://github.com/pdoc3/pdoc)という自動作成ツールが簡単でデザインもそこそこだったので使ってみました。

成果物はこちらです。

https://atu4403.github.io/adash/adash/


## pdoc3とは

pdocからforkしたパッケージでpdocとは別モノです。

本家(pdoc)
[mitmproxy/pdoc: API Documentation for Python Projects](https://github.com/mitmproxy/pdoc)

分家(pdoc3)
[pdoc3/pdoc: Auto-generate API documentation for Python projects](https://github.com/pdoc3/pdoc)

しかし、`pip install pdoc3`でインストールするとcliコマンド`pdoc`をアサインします。つまり競合します。
この２つのプロジェクトはあまり良い関係では無いようです。

[pdoc vs. pdoc3 | PythonRepo](https://pythonrepo.com/repo/mitmproxy-pdoc-python-documentation#pdoc-vs-pdoc3)

しかし現状で`pdoc`はdoctestの表示がうまくいかないようなので、ここでは`pdoc3`を取り上げます。

`pdoc3`はソースコードにdocstringを書いたらそれを元にhtmlのドキュメントを自動作成します。実行は簡単なコマンド一発です。

docstringのstyleはnumpy,google,reST等に対応しています。

## 準備
### docstringを書く

ちゃんとしたプロジェクトならドキュメントにもそれなりの水準が求められますが、弱小プロジェクトなので機能性を優先します。
ドキュメントは関数の種類、引数の型、使用方法があれば十分と思ったので型アノテーションとdoctestだけ書きます。

[typing --- 型ヒントのサポート — Python 3 ドキュメント](https://docs.python.org/ja/3/library/typing.html)

[doctest --- 対話的な実行例をテストする — Python 3 ドキュメント](https://docs.python.org/ja/3/library/doctest.html)


```python
def replace_all(s: str, obj: dict) -> str:
    """複数のreplace
    Example:
        >>> _obj = {"円": ".", "銭": ""}
        >>> replace_all("3円00銭", _obj)
        '3.00'
        >>> _obj = {"[△▲]": "-", "[,、]": ""}
        >>> replace_all('▲12,345', _obj)
        '-12345'
        >>> replace_all('△12、345', _obj)
        '-12345'
    """
    # 実装部分
```

実例はこちら

https://github.com/atu4403/adash/blob/main/src/adash/string_util.py

### READMEを読み込む

docstringだけでは情報が足りないのなら、ドキュメントにREADMEを差し込むことができます。
プロジェクトソース直下の`__init__.py`の冒頭にコメントを追加します。

```python
"""
.. include:: ../../README.md
"""
# 以下普通に実装
from foo import bar
```

`../../README.md`は`__init__.py`からの相対パスです。私はsrcレイアウトを使っているのでこうなりますが、そうでないなら`../`が減ります。


### pdoc3のinstall

```bash
pip install pdoc3
```

## ドキュメントの作成


```bash
pdoc --html --output-dir docs --force src/{プロジェクト名}
```

これを実行するとdocsディレクトリにhtmlのドキュメントが作成されます。

srcレイアウトで無いのなら`src/`は不要です。

ここまで問題なければgithubにpushします。

## github pagesの操作

pushしたリポジトリのgithubのページから、settings → pagesを開き、Branchとdocsを選ぶだけです。

![alt](/images/pdoc/pdoc_01.png)

これで完了のはずですが、表示されたリンクを開いたら404になりました。
どうやらpdocでhtmlを作成した際に、srcレイアウトの弊害でdocsの下にもう１階層できてしまったようです。

```bash
.
├── LICENSE
├── README.md
├── docs
│   └── adash
│       ├── download.html
│       ├── index.html
│       ├── proportion.html
│       └── string_util.html
├── poetry.lock
├── pyproject.toml

```

そのままURLの階層を深くするとちゃんとアクセスできるようなので、特に修正はしません。

URLがちょっと気持ち悪いですが、今回作成したドキュメントがこちらです。

https://atu4403.github.io/adash/adash/


## 自動化
(2021/11/06追記)

上記の手順ではドキュメント更新時にその都度`pdoc`コマンドで作成し、commit & pushする手間があります。これをgithub actionsで自動化します。

https://github.com/peaceiris/actions-gh-pages

このActionを使うと、指定したディレクトリを`gh-pages`ブランチにデプロイします。

`.github/workflows/deploy.yml`を作成します。

```yml
name: GitHub Pages

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-python@v2
        with:
          python-version: 3.9
      - name: Build
        run: |
          pip install pdoc3
          pdoc --html --output-dir tmp --force src/{ここにproject-name}

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.ref == 'refs/heads/main' }}
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./tmp
```

`ここにproject-name`の部分をプロジェクト名に合わせて書き換えます。

以上でpush時に自動で、`gh-pages`ブランチにドキュメントが作成されます。

settings → pages の設定をgh-pagesとrootに変更すると完了です。

