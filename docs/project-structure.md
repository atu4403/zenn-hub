# プロジェクト構成

## Zenn公式ディレクトリ
- `articles/` - Zenn記事ファイル
- `books/` - Zenn本ファイル  
- `images/` - 記事用画像

## カスタム追加ディレクトリ
- `.claude/commands/` - Claude Code自動化コマンド
- `docs/specs/` - 記事生成用仕様書
- `ready-articles/` - 変換待ち記事
- `ready-books/` - 変換待ち本（将来用）

## ワークフロー
1. Obsidianで記事執筆
2. `ready-articles/`に配置
3. Claude Codeで変換・移動
4. `articles/`から公開
