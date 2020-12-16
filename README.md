# jun1miz.github.io


* ピンフレーム あまり使われない
* ピンヘッダ
* ピンソケット

```js
function () {}
```

# 開眼! JavaScript 

補習

```js
var Person1 = function (living, age, gender) {
    // thisはここで生成される新たなオブジェクト(つまり、this = new Object();)
    this.living = living;
    this.age = age;
    this.gender = gender;
    // 関数がnew演算子とともに呼ばれた場合、return文が宣言されていなくてもthisを返す
    // ⇒ 直接コールされた場合は何も返さない(undefined
}

// ひょっとしてこんな感じに書き換えてもいいのでは！？
// 但し、Person1 と同じように動作はしない ※直接コールされた場合もオブジェクトを返す
var Person2 = function (living, age, gender) {
    var self = {};
    self.living = living;
    self.age = age;
    self.gender = gender;
    return self;
}

// これでもいいのでは！？
var Person3 = function (living, age, gender) {
    return {
        living,
        age,
        gender
    };
}

// 残念ながら Person2, Person3 は正しく動作しない

```