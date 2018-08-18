# Dockerイメージを作成してみる

自分でオリジナルのイメージを作成してみます。
1から作るのではなく、各ディストリビューションやプログラミング言語の公式が
Dockerイメージを用意してくれているので、それを元にカスタマイズします。

(最近はアプリケーションの開発元がDockerイメージを用意していることがほとんどなので
作る機会はあまり無いかもしれません。)

## 自作Dockerイメージでhello world

DockerのイメージはDockerfileという専用のファイルに作成手順を記載し、それを元に
ビルドすることで作成できます。
以下、Ubuntu18.04をベースにして`hello world`という出力をするイメージを
作成してみます。

- Ubuntu 18.04をベースにする
-

```sh
mkdir miyakotech
cd miyakotech
vim Dockerfile  # nano Dockerfile でも
```

```dockerfile
FROM ubuntu:18.04
MAINTAINER NAME <mail@address>
RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get -y updrade
ENTRYPOINT echo hello world
```

```sh
docker build -t miyakotech/example .
```

```
Sending build context to Docker daemon  73.22kB
Step 1/3 : FROM ubuntu:18.04
18.04: Pulling from library/ubuntu
c64513b74145: Pull complete
01b8b12bad90: Pull complete
c5d85cf7a05f: Pull complete
b6b268720157: Pull complete
e12192999ff1: Pull complete
Digest: sha256:3f119dc0737f57f704ebecac8a6d8477b0f6ca1ca0332c7ee1395ed2c6a82be7
Status: Downloaded newer image for ubuntu:18.04
 ---> 735f80812f90
Step 2/3 : MAINTAINER NAME <mail@address>
 ---> Running in c13d743b6c23
Removing intermediate container c13d743b6c23
 ---> 86400ff9d53b
Step 3/3 : ENTRYPOINT echo hello world
 ---> Running in 7b3a4b55f72a
Removing intermediate container 7b3a4b55f72a
 ---> 97833eda2cac
Successfully built 97833eda2cac
Successfully tagged miyakotech/example:latest
```


```sh
docker run --rm -it miyakotech/example
```

```
hello world
```

## Dockerfile内の記述

よく使う記述を記載します。
詳細は[公式サイト](https://docs.docker.com/engine/reference/builder)を

#### `FROM`

元になるイメージを指定します

#### `MAINTAINER`

作った人の名前を書き込んでおきます

#### `RUN`

ビルド時に実行するコマンドを指定します。何かをインストールしたり
ファイルを書き換えたりするのに使います

#### `ADD` or `COPY`

ファイルをイメージ内に配置するときに使用します。
通常は `ADD` で良いと思います（[違い](https://qiita.com/hihihiroro/items/0956326d6731bc927166#%E5%B7%AE%E5%88%86)）

#### `ENV`

コンテナ内の環境変数を指定します。
Dockerで稼働するアプリケーションでは環境変数により振る舞いを
変更する（接続先DBのホスト名を指定したり、初期パスワードを指定したり）
ことがよく行われます。

#### `EXPOSE`

公開するポートを指定します。例えばWebサーバーだったら `80` と `443` を指定します。

#### `ENTRYPOINT` / `CMD`

コンテナとして起動したときに実行させるコマンドを指定します
これが肝で、 `echo hogehoge` など、実行するとすぐに終了するコマンドを
指定するとコンテナは即時実行、即時終了します。
`ping localhost` など、ずっと動き続けるコマンドを指定すると、
そのコンテナは動き続けます。

##### `ENTRYPOINT` と `CMD` の違い

- `ENTRYPOINT` は `docker run` するときにコマンドを上書き出来ず、引数として与えられるようになります
- `CMD` は `docker run` するときにコマンドそのものを上書きできます

通常は `ENTRYPOINT` を使うようにしたほうが判りやすいと思います。

**例**

```sh
# ENTRYPOINT に ping とした場合
docker run --rm -it hogehoge hostname   # -> ping hostname が実行される

# CMD に ping とした場合
docker run --rm -it hogehoge hostname   # -> hostname が実行される
```
