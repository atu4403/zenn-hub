---
title: "homebrewでインストールしたpyenvの初期設定"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["python", "pyenv"]
published: true
---

pyenvの使い方を色んなサイトでググってみたのですが、設定がうまく行かず悩みました。
よくある解説は以下のようなものです。

```bash
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bash_profile
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bash_profile
```

要するに、`~/.pyenv/bin`にパスを通すということなのですが無いのです。
pyenvをインストールしてもこのディレクトリは存在しません。

## 環境

macos: 10.14.6
shell: zsh 5.8
pyenv: 2.0.6

pyenvはhomebrewでインストールしました



## 修正方法

公式を見てみました。

https://github.com/pyenv/pyenv

シェルの種類やosごとに設定方法が書いています。

私の場合、shellはzsh、インストールはhomebrewを使いましたので以下のようになっていました。

```bash
echo 'eval "$(pyenv init --path)"' >> ~/.zprofile

echo 'eval "$(pyenv init -)"' >> ~/.zshrc
```

以上で無事に設定できました。

:::details 詳しくはこちら。


つまり以下の工程を行います。

1. `pyenv init --path`の出力を`.zprofile`に書き込む
2. `pyenv init -`の出力を`.zshrc`に書き込む

`pyenv init`コマンドは文字列を出力するだけで設定操作はしないようです。
それぞれの内容を見てみましょう。

```bash
> pyenv init --path
export PATH="/Users/{ユーザー名}/.pyenv/shims:${PATH}"
```

```bash
> pyenv init -
export PYENV_SHELL=zsh
source '/usr/local/Cellar/pyenv/2.0.6/libexec/../completions/pyenv.zsh'
command pyenv rehash 2>/dev/null
pyenv() {
  local command
  command="${1:-}"
  if [ "$#" -gt 0 ]; then
    shift
  fi

  case "$command" in
  activate|deactivate|rehash|shell)
    eval "$(pyenv "sh-$command" "$@")"
    ;;
  *)
    command pyenv "$command" "$@"
    ;;
  esac
}
```

つまり、`.zprofile`にパスを書き込み、`.zshrc`にcompletions(補完設定)を行います。
この環境では`~/.pyenv/bin`にパスを通す必要はないようです。

それでは他の環境で必要な`pyenv init`(オプションなし)には何が書かれているのでしょうか。

```bash
> pyenv init

# (The below instructions are intended for common
# shell setups. See the README for more guidance
# if they don't apply and/or don't work for you.)

# Add pyenv executable to PATH and
# enable shims by adding the following
# to ~/.profile and ~/.zprofile:

export PYENV_ROOT="$HOME/.pyenv"
export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init --path)"

# Load pyenv into the shell by adding
# the following to ~/.zshrc:

eval "$(pyenv init -)"

# Make sure to restart your entire logon session
# for changes to profile files to take effect.
```

`$PYENV_ROOT/bin`にパスが追加されています。

インストーラーやgithubから直接cloneする場合は本体がまるごと`~/.pyenv/`に置かれますが、homebrewの場合は`/usr/local/Cellar/`に設置されます。`pyenv install`で入れたpythonは`~/.pyenv/shims`に設置されるので、ここにパスが通れば良いというわけです。

:::

## まとめ

おそらくhomebrewでインストールした際にちゃんと設定方法が出ていたと思いますが見ていませんでした。

わからない時は公式を見ましょう。
