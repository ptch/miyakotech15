# Wordpressを起動してコンテナ間の連携を確認してみる

実践的なハンズオンとしてWordpressを起動してみます。

WordpressはPHPで書かれたWebサーバーと、MySQLデータベースが連携して稼働しますので、
Wordpressが稼働するコンテナとMySQLが稼働するコンテナが必要になります。

dockerコマンドで1つずつ実行する方法を実施した後に、docker-composeで簡単に実行
させる方法を実践していきます。

## dockerコマンドで1つずつ

### MySQLの起動

WordPressを起動するために、先にMySQLが稼働していないといけないのでMySQLの
コンテナを起動します。起動のさせ方はDocker Hubにあります。

MySQLの公式イメージ : https://hub.docker.com/_/mysql/

```sh
docker run --name miyakotech_mysql -e MYSQL_ROOT_PASSWORD=miyakotech15 -d mysql:8
```

### Wordpressの起動

```sh
docker run --name miyakotech_wordpress -e WORDPRESS_DB_PASSWORD=miyakotech15 -p 80:80 --link miyakotech_mysql:mysql -d wordpress
```

これで http://localhost/ にアクセスすると新しいWordpressが開くはずなのでセットアップを済ませて
動作しているか確認してみましょう。

#### コンテナ内の状態チェック

今Wordpressが起動するところは確認しましたが、はたして本当にこれだけで連携が出来ているのか？

```sh
docker exec -it miyakotech_mysql mysql -uroot -pmiyakotech15

show databases;
use wordpress;
show tables;
select * from wp_users;
```

これでWordpressのセットアップ時に作ったユーザー名が表示されていたらOKです。

## docker-composeで一発起動

docker-composeは今まで手で実行してきた操作をyamlで予め定義しておくことで

```sh
docker-compose up
```

だけで起動出来る便利ツールです。


# [次へ](./04-createimage.md)
