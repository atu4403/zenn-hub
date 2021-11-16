---
title: "git restoreで他のブランチからファイル単位で取り込む"
emoji: "🙌"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['git']
published: true
---

git restoreはgit v2.23.0から実験的に実装されたコマンドです。

`git checkout`から２つのコマンドが派生しました。
- `git switch` : ブランチを切り替える
- `git restore`: ファイルを復元する

※ `git checkout`がなくなる訳ではありません

https://github.blog/2019-08-16-highlights-from-git-2-23/

ちなみにタイトルで「他のブランチからファイルを取り込める」と書きましたが、「他のコミット」と言った方が正確かと思います。

また、直訳だと「復元」ですが、今回は「取り込む」と表します。

![image](https://user-images.githubusercontent.com/73111778/141778921-bea360d8-7fb3-440a-b48a-fdeeb54e367e.png)

> この記事ではエイリアスを使用しています。
> 
> ```bash
> git config --global alias.co checkout
> git config --global alias.br branch
> ```
> 
> この設定により、`git checkout`は`git co`と省略できます。
> `git branch`は`git br`です。


## 下準備

実際にrestoreを試してみる為の下準備をします。

restore1, restore2ブランチを作成します。

```bash
git br restore1
git br restore2
```

restore1ブランチに移動してtest1.txtを作成します。

```bash
git co restore1
```

```txt
1: alice
2: bob
3: charlie
4: dog
5: 👽
6: 😺
```

内容について意味はありません。作成できたらadd & commitしておきます。

次にrestore2ブランチに移動してtest1.txt,test2.txtを作成します。

```bash
git co restore2
```

test1.txt

```txt
test 1
```

test2.txt

```txt
test 2
```

作成できたらadd & commitしておきます。
これで下準備は終了です。

## restoreコマンドの実行
### case 1

restore1ブランチに移動して実行します。

```bash
git co restore1
git restore -s restore2 test1.txt
git restore -s restore2 test2.txt
```
`-s`は`--source`であり、元のブランチ（またはコミット）を指定します。この例では現在の`restore1`ブランチに、`restore2`ブランチからファイルを取り込むイメージです。


これで以下の結果になります。

- test1.txtは`test 1`に書き換えられる
- test2.txtは新規作成される

この状態はworking directoryの変化であり、stagingやcommitは行われません。

取り消したい時はresetします。

```bash
git reset --hard HEAD
```

---

### case 2

resetして下準備が終わった状態として作業を行います。

restore2ブランチに移動します。

```bash
git co restore2
git restore -s restore1 -p
```

今回はファイル名を付けずに`-p`オプションを付けました。

`-p`は`--patch`であり、`git add`や`git checkout`にもあるオプションです。

インタラクティブに元のファイル全てについて、順番に処理を指定します。
実行するとこのような画面になります。

```
diff --git b/test1.txt a/test1.txt
index 6f670c0..d53f34e 100644
--- b/test1.txt
+++ a/test1.txt
@@ -1 +1,6 @@
-test 1
+1: alice
+2: bob
+3: charlie
+4: dog
+5: 👽
+6: 😺
(1/1) Apply this hunk to worktree [y,n,q,a,d,e,?]? 
```

`[y,n,q,a,d,e,?]`いずれかを入力します。

* y - このハンクを worktree に適用します。
* n - このハンクを worktree に適用しません。
* q - quit; このハンクと残りのハンクのいずれも適用しません。
* a - このハンクとファイル内の後続のハンクをすべて適用します。
* d - このハンクおよびファイル内の後続のハンクを適用しません。
* e - 現在のハンクを手動で編集します。
* ? - ヘルプを表示する


ここで`e`を入力するとエディタが開きます。

```bash
# Manual hunk edit mode -- see bottom for a quick guide.
@@ -1 +1,6 @@
-test 1
+1: alice
+2: bob
+3: charlie
+4: dog
+5: 👽
+6: 😺
# ---
# To remove '-' lines, make them ' ' lines (context).
# To remove '+' lines, delete them.
# Lines starting with # will be removed.
# 
# If the patch applies cleanly, the edited hunk will immediately be
# marked for applying.
# If it does not apply cleanly, you will be given an opportunity to
# edit again.  If all lines of the hunk are removed, then the edit is
# aborted and the hunk is left unchanged.
```
コメントとして書いているように、必要な編集を行います。

* `-`で始まる行を削除するには`半角スペース`にします
* `+`で始まる行を削除するにはそれを削除します
* `#`で始まる行は削除されます(無視されます)

ちょっとわかりにくいので補足します。

`-`で始まる行は「実行すると消される行」です。`-`を付けたまま実行すると消えます。`-`を`半角スペース`に変えると残ります。
`+`で始まる行は「変更する行」です。そのまま実行すると変更を取り込みます。行ごと削除すると取り込まれません。

|   | 残す           | 消す     |
|:--|:--------------|:--------|
| - | 半角スペースに変更 | そのまま    |
| + | そのまま          | 行ごと消す |


今回は以下の状態で保存しました。エディタを閉じます。

```
@@ -1 +1,6 @@
 test 1
+4: dog
+5: 👽
+6: 😺
```

test1.txtは以下のようになりました。

```bash
test 1
4: dog
5: 👽
6: 😺
```
4,5,6行目が取り込まれているのに加え、`test 1`が残っています。

### case 3

ここまでは別ブランチからファイルを取り込みました。ブランチ≒コミットなので、同じブランチの以前のコミットから取り込むことも可能です。

restore1ブランチに移動します。

```txt
1: alice
2: bob
3: charlie
4: dog
5: 👽
6: 😺
```

4,5,6行目がおかしいので修正してcommitします。

```txt
1: alice
2: bob
3: charlie
4: dave
5: ellen
6: frank
```
しかし、4行目は以前のものが正しいと気づきました。

現在のcommitはHEADなので、一つ前のHEAD^を元にrestoreします。

```bash
> git restore -s HEAD^ -p
diff --git b/test1.txt a/test1.txt
index be5eee2..d53f34e 100644
--- b/test1.txt
+++ a/test1.txt
@@ -1,6 +1,6 @@
 1: alice
 2: bob
 3: charlie
-4: dave
-5: ellen
-6: frank
+4: dog
+5: 👽
+6: 😺
(1/1) Apply this hunk to worktree [y,n,q,a,d,e,?]? 
```
eを入力してエディタで編集します。

```bash
@@ -1,6 +1,6 @@
 1: alice
 2: bob
 3: charlie
-4: dave
+4: dog
 5: ellen
 6: frank
```

4行目だけを戻すことができました。

```bash
1: alice
2: bob
3: charlie
4: dog
5: ellen
6: frank
```

## まとめ

### 別ブランチからのファイル移動

別ブランチ(またはコミット)のファイルを取り込みたい場合

```bash
git restore -s ブランチ名 ファイル名
```

### 行指定での取り込み

ファイル名の代わりに`-p`オプションを付ける。

```bash
git restore -s ブランチ名 -p
```

このオプションを付けた場合はファイル指定できないので全ファイルに対して以下を選択する

* y - このハンクをワークツリーに適用します。
* n - このハンクを worktree に適用しません。
* q - quit; このハンクと残りのハンクのいずれも適用しません。
* a - このハンクとファイル内の後続のハンクをすべて適用します。
* d - このハンクおよびファイル内の後続のハンクを適用しません。
* e - 現在のハンクを手動で編集します。
* ? - ヘルプを表示する

e(edit)の場合、下記のルールで編集する

```bash
# To remove '-' lines, make them ' ' lines (context).
# To remove '+' lines, delete them.
# Lines starting with # will be removed.
```

|   | 残す           | 消す     |
|:--|:--------------|:--------|
| - | 半角スペースに変更 | そのまま    |
| + | そのまま          | 行ごと消す |

