# Zenn記事公開ワークフロー - ユーザーガイド

ObsidianからZennへの記事公開を自動化するワークフローの使用方法を説明します。

## 📋 前提条件

### 必要なツール
- **Obsidian** - 記事作成
- **VSCode（Windsurf等も可）** - 開発環境
- **Claude Code** - AI支援コマンド
- **Git** - バージョン管理

### 必要なプラグイン・設定
- **Obsidian**: 
  - Templater プラグイン
  - [obsidian-show-file-path](https://github.com/ravimashru/obsidian-show-file-path) プラグイン
- **Zenn**: GitHub連携機能の設定

### プロジェクト設定
```
zenn-hub/
├── config/
│   └── zenn.json          # Zenn設定（要作成）
├── articles/              # Zenn記事
├── reviews/               # レビューレポート
└── .claude/
    └── commands/          # Claude Code指示書
```

**`config/zenn.json`を作成：**
```json
{
  "username": "あなたのZennユーザー名",
  "baseUrl": "https://zenn.dev",
  "articlePath": "articles",
  "bookPath": "books"
}
```

## 🚀 基本ワークフロー

### 1. Obsidianで記事作成

1. **新規ファイル作成**
2. **記事本文を執筆**（タイトルは仮でも可）
3. **記事完成後、Templaterでフロントマター挿入**
   - `Cmd+P` → `Templater: Open insert template modal`
   - 基本フロントマターが挿入される：
   ```yaml
   title: '記事タイトル自動挿入'
   tags: []
   url: ""
   ```
4. **タイトルを確定・調整**（記事内容に合わせて最適化）
5. **タグを手動設定**
   ```yaml
   tags: ["react", "hooks", "javascript"]
   ```

### 2. ファイルパスをコピー

1. **Obsidian右下のパスをクリック** → クリップボードにコピー
2. パスが `obsidian-show-file-path` プラグインにより表示される

### 3. Zenn形式に変換

1. **VSCodeなどでzenn-hubリポジトリを開く**
2. **Claude Codeを起動**
3. **変換コマンド実行**
   ```
   /convert-zenn-format [クリップボードのパス]
   ```

**実行結果：**
- フロントマターが完全なZenn形式に変換
- `articles/{slug}.md`ファイルが作成
- 公開URLが自動生成

### 4. 記事レビュー

```
/review-article articles/{slug}.md
```

**実行結果：**
- 簡潔な品質スコア表示
- 詳細レポートが`reviews/{slug}_review.md`に保存
- 改善提案の提示

### 5. プレビュー確認

```bash
npx zenn preview
```

ブラウザで記事を確認し、問題がなければ次のステップへ

### 6. 公開設定

記事に問題がなければ、フロントマターを編集：
```yaml
published: true  # falseからtrueに変更
```

### 7. コミット・プッシュ

```
/commit-and-push
```

**実行結果：**
- 記事公開時は確認プロンプトが表示
- 安全なコミット・プッシュが実行
- GitHubに変更が反映され、Zennで記事公開

## 📊 各コマンドの詳細

### `/convert-zenn-format`

**機能：**
- ObsidianファイルをZenn形式に変換
- フロントマター自動生成
- URLとslugの自動設定

**生成されるフロントマター：**
```yaml
title: '記事タイトル'
tags: ["react", "hooks", "javascript"]
url: "https://zenn.dev/username/articles/article-slug"
emoji: "⚡"
type: "tech"
topics: ["react", "hooks", "javascript"]
published: false
slug: "article-slug"
```

### `/review-article`

**機能：**
- 記事品質の多角的評価
- 改善提案の提供
- 詳細レポートの生成

**レビュー項目：**
- 構造・読みやすさ
- 技術的内容の正確性
- コード品質
- SEO・発見性
- 読者体験
- Zenn固有要素

**出力例：**
```
📊 記事品質スコア: 85/100
✅ 公開準備: 完了

📈 強み:
- 実用的なコード例が豊富
- 段階的な説明で理解しやすい
- SEO対策が適切

⚠️ 改善点:
1. クリーンアップ関数の説明追加
2. より実践的な応用例
3. パフォーマンス最適化の観点
```

### `/commit-and-push`

**機能：**
- 安全な記事公開管理
- 適切なコミットメッセージ生成
- 公開状態変更の確認

**公開時の確認例：**
```
⚠️  記事公開の確認

このコミットにより以下の記事が公開されます：

📄 記事情報：
- タイトル：「React useEffectの依存配列を正しく理解する」
- ファイル：articles/understanding-react-useeffect-dependencies.md
- 変更：published: false → true

この記事を公開してもよろしいですか？ (y/N)
```

## 🛠️ トラブルシューティング

### よくある問題

#### 1. パスのコピーができない
- `obsidian-show-file-path`プラグインがインストールされているか確認
- Obsidianを再起動

#### 2. 設定ファイルが見つからない
- `config/zenn.json`が正しく作成されているか確認
- JSON形式が正しいか確認

#### 3. プレビューでエラーが発生
- フロントマターの形式を確認
- 必要なフィールドが全て設定されているか確認

#### 4. プッシュが失敗する
- リモートの変更を取得：`git pull origin main`
- 競合がある場合は解決後に再実行

### エラーメッセージと対処法

| エラー                 | 対処法                        |
|---------------------|-----------------------------|
| `設定ファイルが見つかりません` | `config/zenn.json`を作成       |
| `slugが長すぎます`       | タイトルを短縮するか、手動でslugを調整   |
| `プッシュに失敗しました`     | `git pull`で最新を取得後、再実行 |

## 💡 Tips & ベストプラクティス

### 記事作成のコツ
- **記事完成後にタイトル確定**することで、内容に最適なタイトルを設定
- **タグは3-5個程度**が理想
- **タイトルは具体的**で検索されやすく
- **コード例は動作するもの**を使用
- **構造化**を意識した見出し設定

### ワークフローの効率化
- **テンプレート活用**でフロントマター入力を省力化
- **レビュー結果**を参考に記事品質を向上
- **下書き状態**で複数記事を準備し、まとめて公開

### 品質向上
- **レビュースコア80点以上**を目指す
- **実用的なコード例**を豊富に含める
- **読者の問題解決**に焦点を当てる
- **SEOキーワード**を自然に含める

## 🔄 完全なワークフロー例

```
1. Obsidian記事作成（仮タイトルでも可）
   ↓
2. 記事本文執筆・完成
   ↓
3. Templaterでフロントマター挿入
   ↓
4. タイトル確定・タグ設定
   ↓
5. パスコピー
   ↓
6. /convert-zenn-format 実行
   ↓
7. /review-article 実行 → 改善点確認
   ↓
8. 必要に応じて記事修正
   ↓
9. npx zenn preview で確認
   ↓
10. published: true に変更
    ↓
11. /commit-and-push 実行
    ↓
12. 記事公開完了！🎉
```

## 📚 関連資料

- [Zenn公式ドキュメント](https://zenn.dev/zenn/articles/zenn-cli-guide)
- [Obsidian Templater](https://github.com/SilentVoid13/Templater)
- [Claude Code公式ドキュメント](https://www.anthropic.com/claude-code)

---

このワークフローにより、記事作成から公開まで効率的かつ安全に行えます。何か不明な点があれば、各コマンドの指示書も参照してください。
