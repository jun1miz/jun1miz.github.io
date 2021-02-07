

# Compose

|サブコマンド|説明
|-|-
|up|コンテナの生成/起動
|scale|生成するコンテナ数の指定
|ps|コンテナの一覧表示
|logs|コンテナログ出力
|run|コンテナの実行
|start|コンテナの起動
|stop|コンテナの停止
|restart|コンテナの再起動
|kill|実行中のコンテナの強制停止
|rm|コンテナの削除


## よく使うコマンド

```sh
# ローカルのコンテナを全削除
docker rm -f `docker ps -a -q`

# コンテナの一覧表示
docker ps -a 

# ローカルのイメージを全削除
docker rmi -f `docker images -q`

# イメージの一覧表示
docker images
```

## ファイルフォーマット

Composeファイルフォーマットは 1, 2, 2.x, 3.x の複数バージョンがあるが、現在はバージョン 3.x 

```yml
version: "3"
services:
  # サービス名
  webserver:
    image: php:7.4-apache
    ports:
      - "80:80"
    links:
      - "dbserver:mysql"

  dbserver:
    image: mysql:5.6
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=admin123
      - MYSQL_USER=ai134ufsoe
      - MYSQL_PASSWORD=YgHwfSCt

```