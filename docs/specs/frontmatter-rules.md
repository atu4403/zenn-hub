# フロントマター生成ルール

## Articles用フロントマター

```yaml
---
title: "[ファイル名から拡張子を除いた日本語タイトル]"
emoji: "[記事内容に適した絵文字を1文字]"
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: [記事内容から抽出した技術タグ配列]
published: false # true: 本番 / false: 下書き
---```

## 各項目の生成ルール

### title
- 元のファイル名から `.md` 拡張子を除去
- 日本語のままで問題なし
- 例: `Factorioの回路ネットワーク基礎.md` → `"Factorioの回路ネットワーク基礎"`

### emoji
- 記事の内容や技術領域に適した絵文字を1文字選択
- AIの判断で適切と思われるものを選ぶ
- 技術記事らしい絵文字を推奨（🚀 ⚡ 🔧 💻 📝 など）

### type
- 固定値: "tech" # tech: 技術記事 / idea: アイデア記事
- ヒューマンチェックにより修正するため、コメントは残しておくこと

### topics生成ルール

1. **タイトルの全キーワードを最重要視**
   - 辞書にない技術名でも、タイトルにあれば最優先でタグ化
   - 例：「ClaudeCodeの日本語入力問題」→ `ClaudeCode` は必須タグ

2. **新しい技術・ツールを積極的にタグ化**
   - 辞書にない技術名は「新技術」として積極的にタグ化
   - 既知技術への安易な置き換えを避ける

3. **記事内容から関連技術を抽出**
   - @docs/specs/tags-dictionary.json を参照し、記事で実際に言及されている技術を選択
   - 連想による追加は禁止（記事内容にない技術は選択しない）

4. **最大10個程度**
   - ヒューマンチェック時の選択肢を多めに提供
   - タイトル由来のタグ → 記事内容の技術タグ → 記事性質タグの順で優先

### published
- 固定値: false # true: 本番 / false: 下書き
- ヒューマンチェックにより修正するため、コメントは残しておくこと

## 出力例

```yaml
---
title: "PythonでWebスクレイピング入門"
emoji: "🐍"
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["python", "scraping", "beautifulsoup", "requests", "tutorial"]
published: false # true: 本番 / false: 下書き
---
```

```yaml
---
title: "Factorioの回路ネットワーク基礎"
emoji: "⚙️"
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: ["factorio", "circuit-network", "automation", "game"]
published: false # true: 本番 / false: 下書き
---
```

```yaml
---
title: "VSCodeおすすめ拡張機能2024"
emoji: "💻"
type: "tech"
topics: ["vscode", "extensions", "productivity", "development"]
published: false
---
```
