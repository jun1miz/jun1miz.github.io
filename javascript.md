# JavaScript のメモ

## Function

あらゆる関数は prototype プロパティを持つ

## 組み込みプロトタイプを拡張しない

hasOwnProperty()が使われていないループが存在するとプロトタイプに追加したプロパティが表示される可能性がある。

## call

```js
['a','b','c'].slice(1);  // ['b','c']

Array.prototype.slice.call(['a','b','c'], 1);  // ['b','c']
```

## for

```js
var items = ['a','b','c'];

for (var i = 0; i < items.length; i++)
  console.log(items[i])

// items[3] = undefined で ループを抜けるのでこの書き方でもOK
for (var i = 0; items[i]; i++)
  console.log(items[i]);
```

## parseInt
```js
parseInt('08 hello', 10)
8 // ←変換が出来てしまう
```