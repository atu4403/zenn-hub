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

### topics
- 記事内容から技術キーワードを抽出
- 最大5個程度
- @docs/specs/tags-dictionary.json を参照してください。記事内容からキーワードを抽出し、このリストにあるタグを優先的に選択してください。
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
