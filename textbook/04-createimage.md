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
