---
title: "BeautifulSoupã®parseræ¯è¼"
emoji: "ð§¼"
type: "tech" # tech: æè¡è¨äº / idea: ã¢ã¤ãã¢
topics: ['python','BeautifulSoup']
published: true
---

BeautifulSoupã®parserã`xml`ã¨`lxml`ã®éãã«ã¤ãã¦èª¿ã¹ã¦ã¿ã¾ããã

## ç°å¢


```
python = 3.8.12
beautifulsoup4 = 4.10.0
lxml = 4.6.3
```

## æ¦è¦

BeautifulSoupã§ã¯ç¬¬ï¼å¼æ°ã®parserãæå®ããªããã°æ¨æºã®parserãä½¿ããã¾ãã
æ¨æºã®parserã¨ã¯

- lxmlãã¤ã³ã¹ãã¼ã«ãã¦ããªããã°`html.parser`
- lxmlãã¤ã³ã¹ãã¼ã«ãã¦ããã°`lxml`

lxmlãã¤ã³ã¹ãã¼ã«ããã¤ããããªãã¦ããä»ã®ç¨éã§ç¥ããªãéã«ã¤ã³ã¹ãã¼ã«ããlxmlãä½¿ãããå¯è½æ§ãããã¾ããæç¢ºã«ããããã«ãparserå¼æ°ã¯æå®ããã»ããè¯ãã§ãã

ä»åã¯`html.parser`ã¨`html5lib`ã®æ¤è¨¼ã¯è¡ãã¾ããã

http://kondou.com/BS4/#parser-installation


## æ¤è¨¼ï¼

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

### åé¨å¦ç

ã¤ã³ã¹ã¿ã³ã¹ãæå­ååããã¨ä»¥ä¸ã®éãããããã¾ãã

- lxml: html-bodyã§å²ã
- xml : xmlã¿ã°ãåé ­ã«ä»ä¸ãã

xmlã¿ã°ãããæå­åãæ¸¡ãã¦ããlxmlã®å ´åã¯ãã¯ãhtmlã¿ã°ãè¿½å ããã¾ãã

### èªåä¿®æ­£

æåã«æ¸¡ããæå­åã¯`<a><span>test</a></span>`ã§ãããéãã¿ã°ã®ä½ç½®ãééã£ã¦ãã¾ããBeautifulSoupãéãã¨å±ã«ä¿®æ­£ããã¾ãã

### ã¨ã¤ãªã¢ã¹

å¼æ°ã®`xml`ã¨`lxml-xml`ã¯åãã¤ã³ã¹ã¿ã³ã¹ãè¿ãã¾ãã


## æ¤è¨¼ï¼

xmlã¯`<abc:def>`ã®ããã«ã³ã­ã³ä»ãã®ã¿ã°ãããã¾ãã`abc`ã¯ååç©ºéã§ãã

### ã¿ã°åã®å¤æ

- lxml: ã¿ã°åãå¨ã¦å°æå­ã«å¤æãã¾ã
- xml : namespaceãåé¤ãã¾ã


```xml
<aaa>
   <Abc:Def>ghi</Abc:Def>
   <Xyz:Def>123</Xyz:Def>
   <Abc:Def>jkl</Abc:Def>
</aaa>
```

ãã®ãããªæå­åããBeautifulSoupã§`find_all`ããæã®æåãèª¿ã¹ã¦ã¿ã¾ããã

```python
def test_parser_2():
    s = '<aaa><Abc:Def>ghi</Abc:Def><Xyz:Def>123</Xyz:Def><Abc:Def>jkl</Abc:Def></aaa>'
    soup = BeautifulSoup(s, 'lxml')
    soup2 = BeautifulSoup(s, 'lxml-xml')
    assert soup.find_all('abc:def')[0].get_text() == 'ghi'
    assert str(soup.find_all('abc:def')) == '[<abc:def>ghi</abc:def>, <abc:def>jkl</abc:def>]'
    assert len(soup.find_all('abc:def')) == 2
    assert soup2.find_all('abc:def') == []
    assert soup2.find_all('Abc:Def') == []
    assert soup2.find_all('def') == []
    assert str(soup2.find_all('Def')) == '[<Def>ghi</Def>, <Def>123</Def>, <Def>jkl</Def>]'
    assert soup2.find_all('Def')[0].get_text() == 'ghi'
    assert len(soup2.find_all('Def')) == 3

```

### lxml

`soup.find_all('abc:def')`ã§2ã¤ã®è¦ç´ ãæ½åºã§ãã¾ãã

```xml
[
    <abc:def>ghi</abc:def>,
    <abc:def>jkl</abc:def>,
]
```
ã¿ã°åã¯å°æå­ã«å¤æããã¦ããã®ã§`Abc:Def`ã§ã¯ããããã¾ããã

### xml

`soup2.find_all('Def')`ã§å¨ã¦ã®è¦ç´ ãæ½åºã§ãã¾ãã
ååç©ºéãåé¤ããã¦ãã¦ãå¤§æå­å°æå­ã¯ãã®ã¾ã¾ã§ãã
`abc:def`,`Abc:Def`,`def`ã§ã¯ããããã¾ããã

```xml
[
    <Def>ghi</Def>,
    <Def>123</Def>,
    <Def>jkl</Def>
]
```
## ã¾ã¨ã

|               | lxml         | xml                |
|:--------------|:-------------|:-------------------|
| åé¨å¦ç      | htmlã¿ã°ã§å²ã¾ãã | åé ­ã«xmlã¿ã°ãä»ä¸ããã |
| æ§é ã®èªåä¿®æ­£ | â            | â                  |
| ã¨ã¤ãªã¢ã¹         | ãªã           | lxml-xml           |
| ã¿ã°åã®å¤æ     | å¨ã¦å°æå­ã«   | ååç©ºéãåé¤      |

