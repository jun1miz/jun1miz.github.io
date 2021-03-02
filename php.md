# PHP のメモ

## 言語のクセ

### PHPタグ

``<?php ?>`` または ``<?= ?>`` を使う。  
``<? ?>`` はオプションで無効にできるので使わない方が無難

ファイル終端の ``?>`` 終了タグは任意。 ``include`` や ``require`` を利用する場合は、終了タグを省略する方が無難。

```php
<?php echo 'bar' ?>
// 上記 echo と同じ意味
<?= 'bar' ?>
```

### サポートされる基本型

#### スカラー型

- 論理値(bool)  
 → ``true``, ``True``, ``TRUE``, ``false``, ``False``, ``FALSE`` 大文字小文字に依存しない
- 整数(int)
- 浮動小数点 (float, double も同じ)  
 → double は歴史的背景上残っており float が推奨
- 文字列(string)

#### 複合型

- 配列 (array)
- オブジェクト (object)
- callable
- iterable

#### 特殊型

- リソース(resource)
- ヌル (NULL)

### グローバルスコープが独特。

```php
$a = 1;
function test() {
    echo $a;
}
test();
```
上記は何も出力されない。関数内の ``$a`` はローカル変数。  
グローバル変数にアクセスする方法は２通り。

#### ``global`` キーワードを使う。

```php
$a = 1;
function test() {
    global $a;
    echo $a;
}
test();
```
#### ``$GLOBALS`` 配列を使う。
```php
$a = 1;
function test() {
    echo $GLOBALS['a'];
}
test();
```

## 余裕があれば覚えておいてもいいこと

### 変数を明示的に破棄できる。

#### ``unset()`` 関数を使う

```php
$foo = 'bar';
unset($foo);
```
