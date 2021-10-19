---
title: "BeautifulSoupのparser比較"
emoji: "🧼"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ['python','BeautifulSoup']
published: false
---

BeautifulSoupのparser、`xml`と`lxml`の違いについて調べてみました。

## 環境


```
python = 3.8.12
beautifulsoup4 = 4.10.0
lxml = 4.6.3
```

## 概要

BeautifulSoupでは第２引数のparserを指定しなければ標準のparserが使われます。
標準のparserとは

- lxmlをインストールしていなければ`html.parser`
- lxmlをインストールしていれば`lxml`

lxmlをインストールしたつもりがなくても、他の用途で知らない間にインストールしたlxmlが使われる可能性があります。明確にするためにもparser引数は指定したほうが良いです。

今回は`html.parser`と`html5lib`の検証は行いません。

http://kondou.com/BS4/#parser-installation


## 検証１

```python
from bs4 import BeautifulSoup

def test_parser():
    s = '<a><span>test</a></span>'
    soup = BeautifulSoup(s, 'lxml')
    soup2 = BeautifulSoup(s, 'xml')
    assert str(soup) == '<html><body><a><span>test</span></a></body></html>'
    assert str(soup2) == '<?xml version="1.0" encoding="utf-8"?>\n<a><span>test</span></a>'
    assert soup != soup2
    assert soup2 == BeautifulSoup(s, 'lxml-xml')
```

### 内部処理

インスタンスを文字列化すると以下の違いがわかります。

- lxml: html-bodyで囲む
- xml : xmlタグを先頭に付与する

xmlタグがある文字列を渡しても、lxmlの場合はやはりhtmlタグが追加されます。

### 自動修正

最初に渡した文字列は`<a><span>test</a></span>`であり、閉じタグの位置が間違っています。BeautifulSoupを通すと共に修正されます。

### エイリアス

引数の`xml`と`lxml-xml`は同じインスタンスを返します。


## 検証２

xmlは`<abc:def>`のようにコロン付きのタグがあります。`abc`は名前空間です。


### namespace(名前空間)

- lxml: namespaceの処理はありません
- xml : namespaceを削除します

### 大文字小文字の区別

- lxml: 大文字小文字を区別しません
- xml : 大文字小文字を区別します

```xml
<aaa>
   <Abc:Def>ghi</Abc:Def>
   <Xyz:Def>123</Xyz:Def>
   <Abc:Def>jkl</Abc:Def>
</aaa>
```

このような文字列からBeautifulSoupで`find_all`する時の挙動を調べてみました。

```python
def test_parser_2():
    s = '<aaa><Abc:Def>ghi</Abc:Def><Xyz:Def>123</Xyz:Def><Abc:Def>jkl</Abc:Def></aaa>'
    soup = BeautifulSoup(s, 'lxml')
    soup2 = BeautifulSoup(s, 'lxml-xml')
    assert soup.find_all('abc:def')[0].get_text() == 'ghi'
    assert len(soup.find_all('abc:def')) == 2
    assert soup2.find_all('abc:def') == []
    assert soup2.find_all('Abc:Def') == []
    assert soup2.find_all('def') == []
    assert soup2.find_all('Def')
    assert soup2.find_all('Def')[0].get_text() == 'ghi'
    assert len(soup2.find_all('Def')) == 3
```

### lxml

`soup.find_all('abc:def')`で2つの要素が抽出できます。

```python
[
    <Abc:Def>ghi</Abc:Def>
    <Abc:Def>jkl</Abc:Def>
]
```
大文字小文字を区別しないので、`abc:def`でfind_allできます。

### xml

`soup2.find_all('Def')`で全ての要素が抽出できます。
名前空間が削除されていて、大文字小文字を区別しているのがわかります。
('abc:def','Abc:Def','def'の要素は存在しないとみなされます)

## まとめ

|              | lxml         | xml                |
|:-------------|:-------------|:-------------------|
| 内部処理     | htmlタグで囲まれる | 先頭にxmlタグが付与される |
| 自動修正     | ○            | ○                  |
| エイリアス        | なし           | lxml-xml           |
| 名前空間     | 処理なし       | 削除               |
| 大文字小文字 | 区別しない      | 区別する             |
