# Laravel

開発環境は Docker で構築できる

開発するときは node.js (npm) が必要

- MailHog とは ⇒ Go言語で開発されたメールテスト用簡易SMTPサーバ？ localhost:8025で起動してる
- artisan とは ⇒ Laravel用の操作コマンドラインインタフェース。 コントローラの生成など
- composer とは ⇒ PHPのパッケージ管理システム。
- sail とは ⇒ LaravelのDocker開発環境を操作するためのコマンドラインインタフェース
- Blade とは ⇒ Laravelのテンプレートエンジン
- Eloquent とは ⇒ ORマッパー

参考サイト:https://biz.addisteria.com/laravel_basic2/

```sh
# Docker がインストールされた状態で実行 (example-appは任意の名前)
# 大量のダウンロードが始まる
# パーミッションを設定するために　sudo のパスワードを求められる。
curl -s https://laravel.build/example-app | bash

# この処理が終わると docker イメージがローカルに保存されている
# コンテナはまだ作成されていない
cd example-app

# sail コマンドでDockerコンテナが作成され、サービス起動まで一気に実行される
# 完了するまで結構な時間がかかる
# 完了すると MailHog、MySQL、Redis、HTTPサーバのサービスが起動する
./vendor/bin/sail up

# composer コマンドを使う場合は コンテナ上で sail ユーザで実行した方がよさそう
# ⇒ npm や artisan コマンドも同様？
docker-compose exec laravel.test bash
su - sail
cd /var/www/html

# (よくわからんが) autisan でマイグレートを実行する 
# データベースとテーブルが作成される
# ⇒MySQLへの接続先は .env を参照
php artisan migrate

# MySQLにログインして中身を確認したい場合はこんな感じ
# ※もちろん laravel.test からは exit してから実行
# docker-compose exec mysql bash
# mysql

# npm でいろいろ node_modules にインストールして
npm install 
# (よくわからんが) Laravel Mix というものを実行する
# サービスを再起動しないと上手くいかないことがある⇒なぞ
npm run dev

# (よくわからんが) composer で Laravel ui をインストールする 
composer require laravel/ui

# (よくわからんが) autisan で認証機能のひな形ファイルを作成する
php artisan ui bootstrap --auth

# (よくわからんが) npm でインストールして Laravel Mix というものを実行する
npm install && npm run dev

```

./routes/web.app でルーティングを定義

./resources/views にビューファイルを設置

⇒命名規約：*.blade.php

```sh
# Http/Controllersの配下に TestsController.php が作成される
php artisan make:controller TestsController

# tests という名前のテーブル用にマイグレーションファイル作成
# database/migrations/2021_02_03_211018_create_tests_table.php
php artisan make:migration create_tests_table

# マイグレートを実行
# マイグレーションファイルで行った変更を MySQLに反映してね
php artisan migrate

# モデルファイル作成
# -m でマイグレーションファイルも作成
# Models/Post.php
php artisan make:model Post -m

# tinkerコマンドワールドを実行
php artisan tinker

>>> quit
```

## hasOne / belongsTo メソッド

テーブル間で1対1の関係を構築する

例) Userテーブル、Addressテーブルの関係で表す

### 主となる側 ⇒ hasOne メソッド

```php
// User.php 
// Addressを取得できるようにする
public function address() {
    return $this->hasOne(Address:class);
}

# 使い方
$user = User::find($id);
$user->address->address;
```
### 従となる側 ⇒ belongsTo メソッド

```php
// Address.php
// Userを取得できるようにする
public function user() {
    return $this->belongsTo(User::class);
}

# 使い方
$address = Address::find($id)
$address->user->name;
```

## hasMany メソッド

テーブル間で1対多の関係を構築する

例) Divisionテーブル、Userテーブルの関係を表す

```php
# Division.php
# Userリストを取得できるようにする
public function users() {
    return $this->hasMany(User::class);
}

# 使い方
$division = Division::find($id);
foreach ($divison->users as $user) {
    $user->name;
}

# User.php
# UserからDivisonは1つなのでbelongsToを使う
public function division() {
    return $this->belongsTo(Division::class);
}
```
