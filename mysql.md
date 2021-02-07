# MySQL

## mysql コマンド

```sh
mysql -u <user> -p<password> <database>

# データベース一覧
mysql> show database;

# カレントデータベースの表示
mysql> select database();

# カレントデータベースの変更
mysql> use larave_app;

# カレントデータベースのテーブル一覧
mysql> show tables;
```