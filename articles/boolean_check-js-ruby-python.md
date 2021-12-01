---
title: "ruby, javascript, pythonの真偽判定"
emoji: "🔍"
type: "tech"
topics: ["ruby", "javascript", "python"]
published: true
---

普段はjsやpythonで遊んでいるのですが、先日rubyを触ってみた所ハマりました。

原因は真偽判定でした。rubyは`0`を`true`と評価するのを知らなくて原因特定に時間がかかりました。

```ruby
a = 0
if a
  puts 'OK'
else
  puts 'NG'
end
# -> OK
```

せっかくなのでruby, javascript, pythonの真偽判定についてまとめておきます。

## 環境

- ruby: 2.7.4p191
- nodejs: v16.13
- python: 3.8

## 真偽判定一覧

|             | ruby | js | python |
|:------------|:----:|:--:|:------:|
| 1           |  ○   | ○  |   ○    |
| 0           |  ○   | ×  |   ×    |
| 文字列      |  ○   | ○  |   ○    |
| 空文字列    |  ○   | ×  |   ×    |
| 配列        |  ○   | ○  |   ○    |
| 空の配列     |  ○   | ○  |   ×    |
| 連想配列    |  ○   | ○  |   ○    |
| 空の連想配列 |  ○   | ○  |   ×    |

trueは○、falseは×にしています。

## 判定コード

### ruby

```ruby
require 'test/unit'

class TestBool < Test::Unit::TestCase
  def test_bool
    assert_equal !!1, true
    assert_equal !!0, true
    assert_equal !!'foo', true
    assert_equal !!'', true
    assert_equal !![1,2,3], true
    assert_equal !![], true
    assert_equal !!{}, true
  end
end
```

### js(nodejs)

```js
const assert = require('assert');
assert.equal(Boolean(1), true);
assert.equal(Boolean(0), false);
assert.equal(Boolean('foo'), true);
assert.equal(Boolean(''), false);
assert.equal(Boolean([1, 2, 3]), true);
assert.equal(Boolean([]), true);
assert.equal(Boolean({a: 1}), true);
assert.equal(Boolean({}), true);
```

### python

```python
assert bool(1) is True
assert bool(0) is False
assert bool("foo") is True
assert bool("") is False
assert bool([1, 2, 3]) is True
assert bool([]) is False
assert bool({"a": 1}) is True
assert bool({}) is False
```

## ちなみに

shell script（bash, zsh, fish等）では0がtrue、それ以外はfalse
ですので気を付けて。
