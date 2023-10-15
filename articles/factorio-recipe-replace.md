---
title: "Factorioレシピカスタマイズの道: 自作MODでゲームをもっと楽しむ方法"
emoji: "🖥️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["factorio", "lua", "game"]
published: true
---

## はじめに

Factorioは、プレイヤーが自動化された工場を構築し、資源を採掘し、製品を生産し、技術を研究しながらエイリアンの脅威に対抗する、サンドボックス型のリソースマネージメントと工場建設ゲームです。

FactorioのMODは、ゲームプレイの要素を拡張、変更、または改善し、プレイヤーに新しい機能、資源、技術、または挑戦を提供するための拡張機能やツールを提供します。

MODはゲームを楽しく拡張してくれるものですが、MODの相性やプレイスタイルによっては問題が起こることがあります。私は「krastorio2」というMODを導入し、バイターの巣が発生しない設定でゲームを開始しました。

数時間プレイしたところで重大な問題に気付きました。それは、「軍事サイエンスパックを作成することができない」ということです。

軍事サイエンスの作成には「バイター研究データ」が必要となります。そして、「バイター研究データ」の作成には「バイオマス」が必要となります。

しかし、バイオマスはバイターの巣を破壊して手に入れるものなので、バイターの巣がない設定ではバイオマスが得られないのです。

このような状況での解決策は何か？それは、自作のMODを作成し、レシピを変更することです。今回は、「バイター研究データ」のレシピを変更する具体的な方法をステップバイステップで解説します。

この記事を通じて、Factorioのレシピを自分好みに修正し、ゲーム体験をさらにカスタマイズする方法を学べます。それでは、さっそくMOD制作の旅に出発しましょう！

### 概要

- 自作のMODを作成し、ゲームに適用します。
- 本記事はPC版を前提としています。Switch版では基本的に自作のMODを導入することはできないため、対象外となります。
- MODを作成するにはLua言語が必要ですが、今回紹介する方法はLua言語の基本的な知識のみで実行できるため、Luaに限らず一般的なプログラミングの知識があれば問題ありません。

### Factorio-Stdlibについて

**Factorio-Stdlib**は、多くのMODで使用されているライブラリです。もし、あなたがFactorioのMODに興味を持っているのであれば、おそらくすでにインストールしているでしょう。

このライブラリを利用することで、レシピの変更を簡単にコーディングできるようになります。

@[card](https://mods.factorio.com/mod/stdlib)

@[card](https://afforess.github.io/Factorio-Stdlib/index.html)

### modsディレクトリについて

FactorioでMODをインストールすると、**modsディレクトリ**に保存されます。また、自作のMODをゲームに適用させるためには、このディレクトリにファイルを配置する必要があります。

modsディレクトリの場所など、詳細については以下のWikiを参照してください。

@[card](https://wikiwiki.jp/factorio/MOD%E3%81%AE%E5%B0%8E%E5%85%A5%E6%96%B9%E6%B3%95)

### 作成の手順

MODを作成するおおまかな手順は以下のとおりです。

1. アイテムIDを調べる
2. 必要なファイルを用意する
3. 自作のMODを有効化する

## 実践編

### 1. アイテムIDを調べる

今回は「バイター研究データ」のレシピを変更するために、「バイター研究データ」と「バイオマス」のIDが必要になります。これらは **Krastorio2** で定義されているアイテムなので、それを調べます。方法は大きく2種類あります。

1. GitHubのソースコードを確認する
2. modsディレクトリにインストールしたzipファイルを解凍して中身を確認する

どちらもソースコードを確認する点では同じですが、GitHubが利用可能であればこちらの方が簡単です。

MODポータルで **Source:** や **Homepage:** のセクションにGitHubなどのリンクがあるか確認してみましょう。

以下はKrastorio2のMODポータルです。

@[card](https://mods.factorio.com/mod/Krastorio2)

ソースコードを参照できる状態になったら、IDを調べます。簡単な方法は **locale** ディレクトリに配置されたファイルを確認することです。

Krastorio2は日本語に対応しているので、GitHubの検索バーに「バイター研究データ」を入力すると見つかります。バイター研究データのIDは **biters-research-data** だということがわかりました。

```bash
bio-fuel=バイオ燃料
biomass=バイオマス
biters-research-data=バイター研究データ
blank-tech-card=空のテクノロジーカード
```

日本語に対応していない場合は `recipe` や `recipes` というディレクトリを探します。これにより各アイテムのデータが見つかります。それっぽいIDや材料が一致する項目などを探します。

```lua
  {
    type = "recipe",
    name = "biters-research-data",
    energy_required = 40,
    enabled = false,
    allow_productivity = true,
    ingredients = {
      { "biomass", 10 },
      { "coke", 5 },
      { "steel-plate", 5 },
    },
    result = "biters-research-data",
    result_count = 5,
  },
```

### 2. 必要なファイルを用意する

今回作成するMODは簡単なものなので、作成するファイルは3つだけです。

`atu4403-fix-recipe` というディレクトリを用意していますが、これはFactorioのMOD全体でユニークな名前にする必要があります。

```bash
.
├── Makefile
└── atu4403-fix-recipe
    ├── data-final-fixes.lua
    └── info.json
```

#### info.json

```json
{
  "name": "atu4403-fix-recipe",
  "version": "0.1.0",
  "title": "atu4403-fix-recipe",
  "author": "atu4403",
  "contact": "",
  "homepage": "",
  "description": "K2でバイオマスがなくても軍事サイエンスを作成できるように調整",
  "dependencies": [
    "base",
    "stdlib",
    "Krastorio2"
  ],
  "factorio_version": "1.1"
}
```

以下は各フィールドの簡単な説明です。

- **name**: FactorioのMODでのIDです。他のMODと一致しないユニークなものにする必要があります。
- **title**: ゲーム内のMOD一覧で表示される名前です。
- **dependencies**: 依存するMODのIDです。

#### data-final-fixes.lua

MODを作成するには様々なファイルを利用できますが、以下のファイルはprototypeの定義に使われます。

- data.lua
- data-updates.lua
- data-final-fixes.lua

それぞれの違いは呼び出されるタイミングです。`data-final-fixes.lua` は、Krastorio2で「バイター研究データ」が定義された後に呼び出されるので、レシピを書き換えることができます。

```lua
local Recipe = require('__stdlib__/stdlib/data/recipe')
Recipe('biters-research-data'):remove_ingredient('biomass')
```

この例では、**biters-research-data** のレシピから **biomass** を削除しています。**Data.Recipeクラス** には以下のようなメソッドがあります。

- Data.Recipe:add_ingredient(normal[, expensive]): 材料を追加する
- Data.Recipe:remove_ingredient(normal, expensive):  材料を削除する
- Data.Recipe:replace_ingredient(replace, normal[, expensive]): 材料を置き換える

詳しくは以下をご参照ください。

@[card](https://afforess.github.io/Factorio-Stdlib/classes/Data.Recipe.html)

#### Makefile

`make deploy` のコマンドで簡単にデプロイするためのMakefileです。単にコピーするだけです。**MODSDIR** や **MYMOD** は状況に応じて書き換えてください。

```bash
MODSDIR = "/Users/atu/Library/Application Support/factorio/mods"
MYMOD = "atu4403-fix-recipe"

deploy:
	cp -r $(MYMOD) $(MODSDIR)
```
### 3. 自作のMODを有効化する

コードをデプロイした後、実際にゲームを起動します。MODの一覧で自作のMODが追加されていることを確認し、それを有効化してください。今回は **stdlib** も使用しているため、別途インストールする必要があります。

![MOD一覧で自作のMODを有効化する](/images/factorio-recipe-replace/2023-10-15-12-11-52.png)

ゲームを再開し、レシピが変更されていることを確認してください。

![変更されたレシピを表示する](/images/factorio-recipe-replace/2023-10-15-11-04-15.png)

## まとめ

この記事では、FactorioのMOD制作の基本的な手順と、特定のレシピ変更の方法を紹介しました。MOD制作は、ゲームの体験をパーソナライズし、コミュニティに貢献する素晴らしい方法です。自作のMODを作成することで、以下のような貢献が可能となります。

- **ゲームのローカライズ**: Factorioのゲームプレイをさらにアクセス可能で楽しいものにするために、異なる言語への翻訳を提供します。
  
- **不具合の解消**: 特定のMODの組み合わせにより生じる問題を解決します。たとえば、`Exotic Industries` MODと他のMODとの間に互換性の問題が生じる可能性があります。自作のMODを作成するか、既存のMODのソースコードをforkし、修正してプルリクエストを作成することで、これらの問題を解決できます。

- **MODパックの作成**: さまざまなMODを組み合わせて推奨のMODパックを作成し、他のプレイヤーに提供します。ここでは主に`info.json`ファイルの作成が必要で、Lua言語のコーディングは不要です。

FactorioのMOD制作に興味を持っているなら、公式ドキュメントやコミュニティフォーラムを利用して、さらに多くのリソースや情報を探求することをお勧めします。そして、もちろん、実際に自分でMODを作成し、テストし、そして共有することで、学習プロセスをさらに進めることができます。

これで本記事は終わりです。FactorioのMOD制作においてこの情報が役立つことを願っています。何か質問やフィードバックがあれば、ぜひコメント欄にお寄せください。Happy modding!
