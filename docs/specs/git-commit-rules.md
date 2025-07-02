# Git コミットルール

## 基本方針

- Claude Codeの自動生成コミットメッセージを基本とする
- 記事公開状態の変更時のみ特別な確認・警告を行う
- `git status` と `git diff` を活用して変更内容を正確に把握する
- 記事ファイルと他のファイルを分離してコミットする

## コミットフロー

### 1. 変更状況の確認
```bash
git status
```

### 2. 分岐処理

#### ケース A: 記事ファイル（articles/*.md, books/*）に変更がない場合
→ **通常のコミット処理**
```bash
git add .
git commit -m "Claude Code自動生成メッセージ"
```

#### ケース B: 記事ファイルに変更がある場合
→ **段階的コミット処理**

1. **非記事ファイルを先にコミット**
   ```bash
   # 記事以外をステージング
   git add . 
   git reset articles/ books/
   
   # 非記事ファイルをコミット（変更がある場合のみ）
   git commit -m "システムファイル更新: [変更内容]"
   ```

2. **記事ファイルの特別処理**
   - 公開状態変更の検出・確認
   - 専用のコミットメッセージで処理

### 記事ファイルの処理詳細

#### 公開状態変更の検出
```bash
# 記事ファイルの差分確認
git diff articles/ books/
```

**検出パターン:**
```diff
# 記事公開
- published: false
+ published: true

# 記事非公開化  
- published: true
+ published: false
```

### 警告・確認フロー

#### 記事公開時（false → true）

```
⚠️  記事公開の確認

このコミットにより以下の記事が公開されます：

📄 記事情報：
- タイトル：「ClaudeCodeの日本語入力問題、完全に理解した」
- ファイル：articles/claudecode-japanese-input-solution.md
- 変更：published: false → true

🔍 変更詳細：
[git diffの該当部分を表示]

この記事を公開してもよろしいですか？ (y/N)
```

#### 記事非公開化時（true → false）

```
ℹ️  記事非公開化の確認

このコミットにより以下の記事が非公開になります：

📄 記事情報：
- タイトル：「記事タイトル」
- ファイル：articles/example.md
- 変更：published: true → false

続行しますか？ (Y/n)
```

## コミットメッセージ規則

### 非記事ファイルのコミット
Claude Codeの自動生成メッセージをそのまま使用
```
feat: コマンド仕様書を追加
docs: プロジェクト構成を更新
fix: slug生成ルールを修正
```

### 記事ファイルのコミット

#### 内容更新のみ（published値変更なし）
```
docs: 記事更新 - 「記事タイトル」

- articles/slug-name.md
- 内容の修正・追記
```

#### 公開状態変更あり

#### 記事公開
```
feat: 記事公開 - 「記事タイトル」

- articles/slug-name.md
- published: false → true
- 記事が一般公開されます
```

#### 記事非公開化
```
feat: 記事非公開 - 「記事タイトル」

- articles/slug-name.md  
- published: true → false
- 記事が非公開になります
```

#### 複数記事の同時変更
```
feat: 複数記事の公開状態変更

公開:
- 「記事タイトル1」(articles/slug1.md)
- 「記事タイトル2」(articles/slug2.md)

非公開:
- 「記事タイトル3」(articles/slug3.md)
```

## 実装例

### git statusの結果例

```bash
$ git status
On branch main
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   .claude/commands/convert-articles.md
	modified:   docs/specs/frontmatter-rules.md
	modified:   articles/example-article.md
	modified:   articles/another-article.md

no changes added to commit (use "git add" or "git commit -a")
```

### 処理フロー実行例

```bash
# 1. 非記事ファイルをステージング・コミット
git add .claude/commands/convert-articles.md docs/specs/frontmatter-rules.md
git commit -m "feat: 記事変換コマンドとフロントマター仕様を更新"

# 2. 記事ファイルの差分確認
git diff articles/

# 3. 公開状態変更を検出した場合の確認・コミット
git add articles/
git commit -m "feat: 記事公開 - 「記事タイトル」

- articles/example-article.md
- published: false → true  
- 記事が一般公開されます"
```

## 対象外のケース

以下の変更は通常のコミットとして処理：
- 記事内容の更新（published値以外）
- 新規記事の追加（published: falseで作成）
- タイトル、emoji、topicsの変更
- 画像ファイルの追加・更新
