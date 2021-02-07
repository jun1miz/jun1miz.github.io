# JavaScript のメモ

## プリミティブ

数値、文字列、ブーリアン、null、undefined

```js
i = 10;
i = Number(10); // これは i = 10 と同じ。プリミティブを返す
i = new Number(10); // これはオブジェクトを返す。プリミティブではない
```

プリミティブをメソッドで呼び出すと一時的にオブジェクトに変換される。

```js
s = "hello";
s.toUpperCase(); // "HELLO"

// オブジェクトに変換されるのでプロパティを追加できるが・・
s.test = 10; 
// 一時的に変換されただけなので当然呼び出せない。
s.test // undefined
```

```js
// 推奨されない組み込みコンストラクタ
var o = new Object();
var a = new Array();
var s = new String();
var n = new Number();
var b = new Boolean();
throw new Error(test);

// 推奨されるリテラルとプリミティブ
var o = {};
var a = [];
var s = '';
var n = 0;
var b = false;
throw Error('test');

// RegExp と Date は例外
// Function もかな？
```

## エラーオブジェクト

Error(), SyntaxError(), TypeError() など

name, message プロパティを持つ。

エラーコンテクストは new なしで関数として呼び出しても、new を使ってコンストラクタとして呼び出しても振る舞いは同じ。

throw は 任意のオブジェクトを指定できる。 エラーコンテクストである必要はない。

```js
try {
  throw {
    name: 'Hoge'
  }
} catch(e) {
  e.name; // 'Hoge'
}
```

## Array

配列の判定は Array.isArray([]) を利用する。 (IE9以上なので安心して使える)

typeof [] は object を返すので使えない

[] instance Array は true を返す

## 正規表現
/a/gm
new RegExp("a", "gm")

- g : グローバル検索
- m : 複数行
- i : 大文字小文字を区別しない

## スコープ

波括弧のブロックはローカルスコープにはならない。
JavaScriptは関数スコープのみ。

## Function

- あらゆる関数は prototype プロパティを持つ
- 関数は実行可能なオブジェクト
- 関数はローカルスコープを提供する

関数は読み取り専用の name プロパティを持ち、その名前が入る

```js
// 名前付き関数式
f = function foo() {};
f.name; // 'foo'

// 名前なし関数式⇒無名関数
f = function () {}
f.name; // 'f' 実装によって異なる

// 関数宣言
function foo() {}
foo.name // 'foo'
```

関数の巻き上げ

```js
function foo() { console.log('global foo') }
function bar() { console.log('global bar') }
function hoistMe() {
  console.log(typeof foo); // 'function'
  console.log(typeof bar); // 'undefined'
  foo(); // 'local foo'
  bar(); // TypeError: bar is not a function
  function foo() { console.log('local foo') }
  // 変数barだけ巻き上げられる。実装は巻き上げられない!
  var bar = function() { console.log('local bar') }
}
```


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