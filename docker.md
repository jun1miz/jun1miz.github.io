# Docker のメモ

## Docker 基本コマンド

```sh
# Docker Hub 上のイメージを検索
docker search mysql

# ローカルイメージを一覧表示
docker images

# コンテナのポート番号を 80 を ホストのポート8080 にマップ
docker run -d -p 8080:80 [イメージ名]

# bash を実行
docker exec -it [コンテナ識別子] /bin/bash

# docker build -t [これから生成するイメージ名]:[タグ名] [Dockerfileの場所]
# 例) これを実行すると /home/docker/sample/Dockerfile をもとに「sample:1.0」イメージが作成される
docker build -t sample:1.0 /home/docker/sample

# -f を付けると任意のDockerfile名が指定できる。
docker build -t sample:1.0 -f Dockerfile.base /home/docker/sample

# コンテナの一覧表示
docker ps -a 

# ローカルのコンテナを全削除
docker rm -f `docker ps -a -q`

# ローカルのイメージを全削除
docker rmi -f `docker images -q`

# コンテナ内のファイルをホストにコピーする
docker cp 3d41745d4c25:/home/sail/laravel-test/composer.phar ~/
```

## Docker Compose コマンド 

```sh
# 管理コンテナをまとめて起動(-d バックグラウンド実行)
docker-compose up -d

# 管理コンテナの動作一覧
docker-compose ps

# 指定したサービス名で bash を実行
docker-compose exec php bash

# コンテナのログ表示(tail -f)
docker-compose logs -f 

# 管理コンテナをまとめて停止
docker-compose stop
```

## Docker Compose フォーマット

Composeファイルフォーマットは 1, 2, 2.x, 3.x の複数バージョンがあるが、現在はバージョン 3.x 

Dockerfile
```yml
# ベースイメージ
FROM php:7.4-apache

# pdo_mysql mysqli をインストール
RUN apt-get update && docker-php-ext-install pdo_mysql mysqli
```

docker-compose.yml

```yml
version: "3"
services:
  # サービス名
  webserver:
    # イメージの構成(Dockerfileの場所)
    build: .
    # コンテナにホストパスをマウント
    volumes: 
      - ./html:/var/www/html
    # 公開ポート(ホスト側:コンテナ側)
    ports:
      - "80:80"
    # コンテナ間のリンク(サービス名:エイリアス名)
    links:
      - "dbserver:mysql"
  # サービス名
  dbserver:
    # ベースイメージ
    image: mysql:5.6
    # コンテナにホストパスをマウント(ホスト側:コンテナ側)
    volumes: 
      - ./mysql-data:/var/lib/mysql
    # 公開ポート(ホスト側:コンテナ側)
    ports:
      - "3306:3306"
    # 環境変数の設定(MySQLの初期値)
    environment:
      - MYSQL_ROOT_PASSWORD=admin123
      - MYSQL_USER=foo
      - MYSQL_PASSWORD=foo123
```