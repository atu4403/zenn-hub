---
title: 'マイナーだけどオススメなvscode機能拡張 2021'
emoji: '👻'
type: 'idea'
topics: ['vscode']
published: true
---

VScodeのオススメ機能拡張です。2021年12月版。

- 100万回以上ダウンロードされてる有名どころは除外してます
- ☁️で始まる数字は2021年12月1日時点でのダウンロード数

## [Bracket Select](https://marketplace.visualstudio.com/items?itemName=chunsen.bracket-select)

☁️ 36,075

括弧やクォーテーション内のテキストをショートカット一発で選択。括弧を含む含まないどっちも可能。マルチセレクトにも対応。

|           |               |
|:----------|:--------------|
| alt+a     | 括弧内を選択   |
| alt+cmd+a | 括弧も含めて選択 |
| alt+z     | 選択を戻す      |

![alt](https://github.com/wangchunsen/vscode-bracket-select/raw/master/bracket-select.gif)

## [Conventional Commits](https://marketplace.visualstudio.com/items?itemName=vivaxy.vscode-conventional-commits)

☁️ 50,294

gitのコミットメッセージ規約である[Conventional Commits](https://www.conventionalcommits.org/ja/v1.0.0/)に従ったコミットメッセージを作成補助するツール。

![alt](https://github.com/vivaxy/vscode-conventional-commits/raw/master/assets/docs/demo.gif)

## [Fish](https://marketplace.visualstudio.com/items?itemName=bmalehorn.vscode-fish)

☁️ 13,763

fish scriptのコードハイライト、lint, formatに対応。別の機能拡張のほうが人気だがこっちのほうが高機能。

## [Incrementor](https://marketplace.visualstudio.com/items?itemName=nmsmith89.incrementor)

☁️ 31,515

数字が書いてある場所でショートカットキーを押すとインクリメント、デクリメントできる。

![alt](https://github.com/nmsmith22389/vscode-incrementor/raw/master/images/by-tenth.gif)

defaultのキー設定。

```json
{
    "command": "incrementor.incrementByOne",
    "key": "ctrl+up"
},
{
    "command": "incrementor.decrementByOne",
    "key": "ctrl+down"
},
{
    "command": "incrementor.incrementByTenth",
    "key": "ctrl+shift+alt+up"
},
{
    "command": "incrementor.decrementByTenth",
    "key": "ctrl+shift+alt+down"
},
{
    "command": "incrementor.incrementByTen",
    "key": "ctrl+shift+up"
},
{
    "command": "incrementor.decrementByTen",
    "key": "ctrl+shift+down"
}
```

## [Insert Date String](https://marketplace.visualstudio.com/items?itemName=jsynowiec.vscode-insertdatestring)

☁️ 51,336

ショートカットキーで日付を挿入できる。

- Insert DateTime (⇧+⌘+I on OS X, Ctrl+Shift+I on Windows and Linux) - Inserts current date and/or time according to configured format (format) at the cursor position.
- Insert Date - Inserts current date according to configured format (formatDate) at the cursor position.
- Insert Time - Inserts current time according to configured format (formatTime) at the cursor position.
- Insert Timestamp - Inserts current timestamp in milliseconds at the cursor position.
- Insert Formatted DateTime (⇧+⌘+⌥+I on OS X, Ctrl+Alt+Shift+I on Windows and Linux) - Prompt user for format and insert formatted date and/or time at the cursor position.

## [Markdown Table Maker](https://marketplace.visualstudio.com/items?itemName=hellorusk.markdown-table-maker)

☁️ 1,130

markdownの表をカンタンに作成。

![alt](https://user-images.githubusercontent.com/36184621/56092677-e6967b00-5ef9-11e9-8487-96bd057549df.gif)

使い方は製作者さんのqiitaを参照。

> [Markdown のテーブルを直感的に生成できる VSCode の拡張機能を作った - Qiita](https://qiita.com/HelloRusk/items/d044e64918fa9bd4c92a)

## [Markdown Table Prettifier](https://marketplace.visualstudio.com/items?itemName=darkriszty.markdown-table-prettify)

☁️ 99,705

markdownの表をカンタンに整形。設定の`format on save`を有効化していれば保存時に整形してくれる。

![Markdown Table Prettifier](https://github.com/darkriszty/MarkdownTablePrettify-VSCodeExt/raw/HEAD/assets/animation.gif)

## [Paste Image](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image)

クリップボード内の画像を保存してmarkdownの画像リンクを生成してくれる。神ツール。

☁️ 145,515

![alt](https://raw.githubusercontent.com/mushanshitiancai/vscode-paste-image/master/res/vscode-paste-image.gif)

## [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)

☁  3,125,461

markdownのリンターです。300万ダウンロードの有名な拡張ですが紹介させてください。

markdownの文法におかしな所があったら警告してくれるツールです。

私自身「markdownにリンターいらないよね」と思っていたのですが、以下のルールにより考えが変わりました。

> [MD014 - Dollar signs used before commands without showing output](https://github.com/DavidAnson/markdownlint/blob/main/doc/Rules.md#md014---dollar-signs-used-before-commands-without-showing-output)

40以上あるルールの一つです。

```bash
$ ls
```

これはルール違反になり警告が出ます。`$`はプロンプトであり、このような記述には不要です。

GitHubやqiitaではコードブロックの右上にコピーできるボタンが付いています。ユーザー(記事やREADMEを見に来た人)はコードブロックをコピーして自分の環境にペーストしたいという需要があります。しかし`$`は実行するには不要であり、ユーザーには取り除く手間が生じます。

実際に色んな方の記事を見ていると、コピペしにくい`$`が付いた例が多く見られます。中でもcliアプリケーションをワンライナーでインストールできるものに付いていたらせっかくのワンライナーが台無しです。

しかし以下の場合はルール違反にはなりません。入力と出力が明確になるので`$`は必要だからです。

```bash
$ ls
foo bar
$ cat foo
Hello world
$ cat bar
baz
```

このような悪習を防ぐためにも、markdownを扱う人には是非入れてほしい機能拡張です。
