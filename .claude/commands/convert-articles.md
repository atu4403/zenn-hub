# Articles変換コマンド

## 処理内容

ready-articles/ ディレクトリ内の単一Markdownファイルを、Zenn Articles形式に変換してarticles/ ディレクトリに移動します。

## 参照する仕様書

- slug生成: @docs/specs/slug-generation.md
- フロントマター生成: @docs/specs/frontmatter-rules.md
- 技術用語変換: @docs/specs/keyword-dictionary.yaml
- タグ選択: @docs/specs/tags-dictionary.json

## 具体的な処理手順

1. **対象ファイルの確認**
   - ready-articles/ 内の .md ファイルを確認
   - 単一ファイルを処理対象とする

2. **ファイル内容の解析**
   - ファイル内容を読み込み
   - ファイル名から日本語タイトルを取得（拡張子除去）
   - 記事内容から技術キーワードを抽出

3. **slug生成**
   - @docs/specs/slug-generation.md の仕様に従ってslugを生成
   - articles/ ディレクトリ内の既存ファイルと重複チェック
   - 重複時は連番を付与（例: `slug-name-2.md`）

4. **フロントマター生成**
   ```yaml
   ---
   title: "[日本語ファイル名（拡張子除く）]"
   emoji: "[記事内容に適した絵文字を1文字選択]"
   type: "tech" # tech: 技術記事 / idea: アイデア記事
   topics: [tags-dictionary.jsonを参照して適切なタグを5個まで選択]
   published: false # true: 本番 / false: 下書き
   ---
   ```

5. **ファイル出力**
   - 生成したフロントマター + 元の記事内容で新ファイル作成
   - articles/[生成されたslug].md として保存
   - 元のready-articles/ 内のファイルを削除

6. **処理結果レポート**
   ```
   ✅ 処理完了
   元ファイル: [元のファイル名].md
   新ファイル: [生成されたslug].md
   生成されたslug: [slug名]
   ```

## 重要な注意事項

- 単一ファイルのみを処理（複数ファイル一括処理は行わない）
- 既存のarticles/ 内ファイルは上書きしない
- 画像ファイルは現時点で対象外
- エラー時は詳細なエラーメッセージを表示
