# Dockerコンテナを実行してみる

DockerをインストールすればDockerのコンテナの実行まであと少しです。

## Dockerの実行権限を付与

ログインユーザー(ubuntu)がdockerを実行できるように設定します

```sh
sudo usermod -aG docker
# -a を忘れるとグループ追加ではなくて置き換えになるので注意
```

実行したら一度再ログインし、以下のコマンドを実行します。

```sh
docker run hello-world
```

以下のようなメッセージが出てくると思います。

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

前半部分(以下)はホスト側のメッセージですhello-world:latestというイメージがローカルに見つからないので、ダウンロードします

```
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
9db2ca6ccae0: Pull complete
Digest: sha256:4b8ff392a12ed9ea17784bd3c9a8b1fa3299cac44aca35a85c90c5e3c7afacdc
Status: Downloaded newer image for hello-world:latest
```

残りの部分(以下)はhello-worldイメージをベースとしたコンテナの実行結果です

```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```

Dockerはデフォルトで Docker Hub https://hub.docker.com/ というリポジトリから
イメージを自動でダウンロードしてきます。今実行した`hello-world`イメージは
https://hub.docker.com/_/hello-world/ です


今の時点でローカルに`hello-world`イメージが保存されているので、もう一度実行してみてください。

```sh
docker run hello-world
```

今度はダウンロードが走らないので一瞬で実行されたと思います。
VirtualBoxなどの仮想マシンでこれと同じことをやろうと思うと、OSの起動から始まるので
この速度で起動させるのは無理であることがはっきり判ると思います。

## コンテナの確認

今の時点で、以下のコマンドを実行すると **コンテナの一覧** が表示されます

```sh
docker ps -a
# -a オプションで停止中のコンテナも表示する
```

`STATUS`を見ると`Exited`になっています。実行が完了したコンテナはこの状態で保管されています。

```sh
docker start -a <コンテナ名>
```

で停止中のコンテナを再起動することができます。今回実行したhello-worldのような
単発実行して終了するだけのコンテナは残しておくとディスク容量がもったいないので
削除しましょう。


```sh
docker rm <コンテナ名>
```

終了しているものを一気に消したければ以下

```sh
docker rm `docker ps -qa`
```


