# MySQL

## mysql コマンド

```sh
$ mysql -u [user] -p[password] [database]

# データベース一覧
show database;

# カレントデータベースの表示
select database();

# カレントデータベースの変更
use larave_app;

# カレントデータベースのテーブル一覧
show tables;

# 使用できる文字コード
show charset;

# 文字コード関連の設定値
show variables like '%char%';
```

データベースの文字コード

* utf8mb4_bin : 英字の大文字小文字を含めて全て区別する。
* utf8mb4_general_ci: 英字の大文字小文字は区別しない。他は全て区別する。
* utf8mb4_unicode_ci: 大文字小文字/全角半角を区別しない。

## mysqldump コマンド

```sh
# 文字化けする場合、バイナリのままダンプを取得する
# ⇒ 文字コードに存在しないデータが登録されている場合は、文字化け「2」してしまう
$ mysqldump --default-character-set=binary -u [user] -p[password] [database] > dump.sql
# 取得したダンプを復元する
mysql -u [user] -p[password] [database] --default-character-set=binary < dump.sql
```
