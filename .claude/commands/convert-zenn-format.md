# convert-zenn-format

## 概要
ObsidianのMarkdown記事をZenn形式に変換し、articlesディレクトリに適切なファイル名で保存するコマンド。

## 実行内容

1. 指定されたObsidianファイルパスのMarkdownファイルを読み取り
2. 既存のフロントマターを解析
3. Zenn用の完全なフロントマターに変換・拡張
4. `articles/{slug}.md` として新しいファイルを作成

## 入力フロントマター形式
```yaml
title: '記事タイトル自動挿入'
tags: ["tag1", "tag2"]  # 手動設定済み
url: ""
```

## 出力フロントマター形式
```yaml
title: '記事タイトル自動挿入'
tags: ["tag1", "tag2"]
url: ""
emoji: "👻"
type: "tech"
topics: ["tag1", "tag2"]
published: false
slug: "article-title-auto-insert-tag1-tag2"
```

## 変換ルール

### emoji
- 記事の内容とタイトルに基づいて適切な絵文字を1つ選択
- 技術記事なら🔧、🚀、💡、⚡、🎯など
- アイデア記事なら💭、🌟、✨、🎨、🔥など

### type
- 固定値: "tech"
- アイデア記事の場合は手動で "idea" に変更

### topics
- tagsの値をそのままコピー
- 配列形式を維持

### published
- 固定値: false（プレビュー確認後に手動でtrueに変更）

### slug
- titleから生成（日本語→英語翻訳）
- tagsを追加（ハイフンで連結）
- 小文字・ハイフン区切り
- **最大50文字まで**（超過する場合は適切に短縮）
- 例: "React Hooksの使い方" + ["react", "frontend"] → "how-to-use-react-hooks-react-frontend"

### url
- 設定ファイルからユーザー名を読み取り、公開URLを自動生成
- 形式: `https://zenn.dev/{username}/articles/{slug}`
- 例: `https://zenn.dev/atu4403/articles/understanding-react-useeffect-dependencies`

## 処理手順

1. 設定ファイル（`config/zenn.json`）からユーザー名を読み取り
2. ファイルパスからMarkdownファイルを読み取り
3. フロントマターをパース
4. 記事本文を分析してemojiを決定
5. titleを英語に翻訳してslugを生成（最大50文字）
6. slugからURLを生成（`https://zenn.dev/{username}/articles/{slug}`）
7. 新しいフロントマターでファイルを更新
8. ファイルを `{slug}.md` としてarticlesディレクトリにコピー
9. 変更内容とファイル移動結果を表示して確認を求める

## ファイル操作

- 元のObsidianファイルは変更せずそのまま保持
- `articles/{slug}.md` として新しいファイルを作成
- Zenn用の完全なフロントマターを含む記事ファイルを生成

## 注意事項

- 既存のtags配列は変更しない
- 設定ファイルが存在しない場合はエラーを表示
- 元のObsidianファイルは変更せず保持
- slugが50文字を超える場合は適切に短縮
- articlesディレクトリが存在しない場合は作成
- 同名ファイルが存在する場合は上書き確認

## 設定ファイル要件

**`config/zenn.json`** (必須)
```json
{
  "username": "atu4403",
  "baseUrl": "https://zenn.dev"
}
```
