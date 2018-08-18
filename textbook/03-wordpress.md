# Wordpressを起動してコンテナ間の連携を確認してみる

実践的なハンズオンとしてWordpressを起動してみます。

WordpressはPHPで書かれたWebアプリケーションと、MySQLデータベースが連携して稼働しますので、
Wordpressが稼働するコンテナとMySQLが稼働するコンテナが必要になります。
（※できるだけ1コンテナ=1プロセスに）

dockerコマンドで1つずつ実行する方法を実施した後に、docker-composeで簡単に実行
させる方法を実践していきます。

## dockerコマンドで1つずつ

### MySQLの起動

WordPressを起動するために、先にMySQLが稼働していないといけないのでMySQLの
コンテナを起動します。起動のさせ方はDocker Hubにあります。

MySQLの公式イメージ : https://hub.docker.com/_/mysql/

```sh
docker run --name miyakotech_mysql -e MYSQL_ROOT_PASSWORD=miyakotech15 -d mysql:5.7
```

### Wordpressの起動

```sh
docker run --name miyakotech_wordpress -e WORDPRESS_DB_PASSWORD=miyakotech15 -p 80:80 --link miyakotech_mysql:mysql -d wordpress
```

これで http://localhost/ にアクセスすると新しいWordpressが開くはずなのでセットアップを済ませて
動作しているか確認してみましょう。

### よく使うdockerコマンド

#### `docker run -it --rm --name=<CONTAINER_NAME> <IMAGE_NAME>`

コンテナを起動するコマンドです。オプションが多く、コマンドが長くなりがちです。

#### `docker ps`

一番よく使うコマンドです。
今起動中のコンテナを一覧表示します。

#### `docker ps -a`

停止中のコンテナを含めて表示するコマンドです。
コンテナがエラーで止まったときや、使用しなくなったコンテナを
確認するときに使用します。

今実行すると hello-world のコンテナが残っているのが判るはずです。

#### `docker logs <CONTAINER_NAMEなど>`

コンテナの中のログを表示します。
`docker logs -f <CONTAINER_NAME>` で `tail -f` が出来ます

#### `docker exec -it <CONTAINER_NAME> <COMMAND>`

コンテナの中に入り込んで直接操作するときに使用します

##### コンテナ内の状態チェック

試しに起動しているMySQLを覗いてみます

```sh
docker exec -it miyakotech_mysql mysql -uroot -pmiyakotech15
```

```sql
show databases;
use wordpress;
show tables;
select * from wp_users;
```

※イメージにsshをインストールするのはあんまりよろしくないです

#### `docker rm <CONTAINER_NAME>`

停止中のコンテナを削除するときに使用します。

停止中のものを一気に削除したい場合は以下の通りに

```sh

```

#### `docker stop <CONTAINER_NAME>`

コンテナを通常停止するときに使用します

#### `docker kill <CONTAINER_NAME>`

コンテナを強制終了するときに使用します

#### `docker images`

イメージの一覧を表示するときに使用します

#### `docker rmi <IMAGE_NAME>`

イメージを削除するときに使用します

#### `docker pull <IMAGE_NAME>`

Docker Hubからイメージをダウンロードだけするときに使用します

#### `docker build .`

現在のディレクトリにある `Dockerfile` を元にイメージをビルドするコマンドです

## docker-composeで一発起動

docker-composeは今まで手で実行してきた操作をyamlで予め定義しておくことで

```sh
docker-compose up
```

だけで起動出来る便利なツールです。
開発環境としてDockerを使う場合はこの仕組みを使うことで各メンバー間で
簡単に統一された実行環境を共有できるようになります。

docker-composeの定義ファイルは `docker-compose.yml` というファイルに記述します。

```sh
vim docker-compose.yml
```

```yaml
version: '3.1'

services:
  wordpress:
    image: wordpress
    restart: always
    ports:
      - 80:80
    environment:
      WORDPRESS_DB_PASSWORD: miyakotech15

  mysql:
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: miyakotech15
```


```sh
docker-compose up -d
```

で起動します。

### よく使うdocker-composeコマンド

#### `docker-compose up`

docker-compose.ymlに定義したコンテナを起動します。
`docker-compose up -d`と、`-d`オプションをつけるとバックグラウンドで稼働します

#### `docker-compose stop`

`up` したコンテナ群を停止します（削除はされません）

#### `docker-compose down`

`up` したコンテナ軍を停止して削除します。

#### `docker-compose logs -f`

コンテナ群のログをまとめて追尾表示します。

### 簡単なサービスをセットアップするときのテク

Linuxのsystemdという仕組みでこのdocker-composeを使うと簡単に
アプリケーションのサービス化が実現できます。

```sh
sudo mkdir -p /etc/docker/compose/wordpress
sudo cp docker-compose.yml /etc/docker/compose/wordpress/docker-compose.yml
sudo vim /etc/systemd/system/wordpress.service
```

```sh
[Unit]
Description=Wordpress service with docker compose
Requires=docker.service
After=docker.service

[Service]
Restart=always
WorkingDirectory=/etc/docker/compose/wordpress
ExecStart=/usr/local/bin/docker-compose up
ExecStop=/usr/local/bin/docker-compose stop

[Install]
WantedBy=multi-user.target
```

```sh
sudo systemctl enable wordpress
sudo systemctl start wordpress
```


# [次へ](./04-createimage.md)
