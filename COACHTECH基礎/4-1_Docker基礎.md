---
tags:
  - DB
---

# Docker

## Docker とは

コンテナ型の仮想環境を作成、共有、操作する技術。  
仮想環境＝擬似的に再現された環境  
<br>

## 開発環境とは

プログラムを書くための材料をそろえること。

- OS(Linux など)
- プログラミング言語(php など)
- Web サーバソフトウェア(NGiNX など)
- データベース(MySQL など)
  を開発ごとにインストールする必要がある。  
  <br>

## 開発環境構築をする方法

- 開発環境を他人から共有してもらう
- 開発環境を自作する

<br>

## Docker のメリット

- 同じ開発環境の共有ができる
- 簡単に開発環境の構築ができる
- 開発環境の管理がしやすい

<br>

### 同じ開発環境の共有

OS やソフトウェアのバージョンが異なると問題が発生する可能性がある。  
Docker を利用すると、コンテナによって統一されたバージョンによるソフトウェアの  
環境構築が可能。  
<br>

### 簡単な開発環境の構築

コンテナを通して統一されたバージョンの複数のソフトウェアを  
簡単なコマンドでインストールできる。  
<br>

### 開発環境の管理

開発を続けていくとパソコンの要領に影響が出てくる。  
Docker を使えば、使わなくなったものはすぐに消せる。

<br><br>

# コンテナとイメージの基本

イメージからコンテナ作成し、各コンテナを Docker が動かす。

- イメージ　＝＞　コンテナを作成させるための情報をまとめたもの
- コンテナ　＝＞　アプリケーションを動作させるために必要なライブラリやアプリケーションを一つにまとめた箱のようなもの  
  <br>

1. ソフトをインストールする（イメージ）
2. インストールしたものを格納する（コンテナ）
3. 起動する（Docker エンジン）
4. プログラムを書くなどして実際に構築する（PC）

<br>

## Docker で使うコマンド一覧

コマンドラインで使うためのコマンド
<br>
|コマンド| 説明|
|-------|------|
|docker pull |イメージのダウンロード|
|docker images |イメージの一覧を表示する|
|docker serch |イメージの検索|
|docker rmi |イメージの削除|
|docker run |コンテナの生成/起動|
|docker ps |起動中のコンテナを確認|
|docker ps -a |コンテナの一覧を確認|
|docker start |コンテナの起動|
|docker stop |コンテナの停止|
|docker restart |コンテナの再起動|
|docker rm |コンテナの削除|
<br>

## コンテナ操作

1. イメージのダウンロード
2. コンテナを作成
3. コンテナを起動

<br>

- イメージのダウンロード
  `$ docker pull [オプション] イメージ名[:タグ名]`  
  <br>

- イメージの一覧を確認する
  `$ docker images`  
  イメージの削除
  `$ docker rmi [オプション] イメージ名`
  <br>

- コンテナの生成/起動
  `$ docker run [オプション] イメージ名[:タグ名] [引数]`  
  <br>

- コンテナの状態確認
  `$ docker ps`　　
  ※起動していない時は何も表示されない  
  全てのコンテナの一覧表示
  `$ docker ps -a`  
  <br>

- コンテナの起動
  `$ docker start コンテナID`  
  <br>

- コンテナの停止
  `$ docker stop コンテナID`  
  <br>

- コンテナの削除
  `$ docker rm コンテナID`  
  <br>

## Docker イメージの保存場所

Docker で使用されるイメージはインターネット上に共有されている。  
イメージを公開・共有しているサービスを「Docker Hub」という。  
https://hub.docker.com/  
<br>
Docker Hub に公開されている一つ一つのイメージを「Docker レジストリ」という。

<br><br>

# 新規イメージ作成

## Dockerfile

イメージを作り上げるために実行するコマンドラインの命令全てを含めたファイル  
Dockerfile で一つのコマンドを実行することで、すべてインストールされる。  
<br>
「Dockerfile 　 ≒ 　環境構築のレシピ」

## コンテナ作成までのフロー

1. Dockerfile を作成する
2. ビルドして、イメージを作成する
3. イメージからコンテナを作成する

<br>

## Dockerfile の作成

Dockerfile を作成するディレクトリに移動する  
<br>

- Dockerfile 作成
  `$ touch Dockerfile`  
  <br>
  同じ階層に html ディレクトリと html ファイル作成

```
$ mkdir html
$ touch html/test.html
```

<br>

- tree コマンドで確認
  tree コマンドでディレクトリ構成を確認できる  
  brew コマンドでインストール  
  `$ brew install tree`  
  <br>

ディレクトリ名を指定すると、そのディレクトリ以下の構成が出力される  
`$ tree dockerfile-practice`

```
dockerfile-practice
    ├── Dockerfile
    └── html
        └── test.html

1 directory, 2 files
```

<br>

## Dockerfile に記述

```
FROM nginx
COPY ./html /usr/share/nginx/html
```

<br>
|構文|	説明|
|FROM イメージ名|	イメージを指定します|
|COPY 元のパス コンテナ内のパス	|パスを指定して、コンテナ内にコピーします|
<br>
nginxというWebサーバソフトウェアのイメージを元にして作成している。  
<br>

## 出力する HTML の作成

<br>

## イメージのビルド

Dockerfile からイメージを作成するための作業  
`$ docker build -t [生成するイメージ名]:[タグ名] [Dockerfileの場所]`  
`$ docker build -t nginx-image .`  
<br>

作成したイメージの確認

```
? イメージ名を指定して確認ができる
$ docker images -a nginx-image

REPOSITORY    TAG       IMAGE ID       CREATED             SIZE
nginx-image   latest    7ef5bc681c02   About an hour ago   141MB
```

上記のようにイメージを作成できていたら完了。

<br>

## イメージからコンテナの作成

イメージからコンテナを作成し、nginx が立ち上がるか確認する。  
`$ docker run -d --name nginx-container -p 80:80 nginx-image`

- -d 　コンテナの実行ログをコマンドラインに出力させないオプション
- --name 　作成するコンテナの名前
- -p 　ポート番号の割り当て

<br><br>

# Compose ファイル

## docker-compose

複数のコンテナを同時に操作や管理できる機能  
docker-compose.yml というファイルにコンテナの設定を記述し、管理する。  
<br>
docker-compose.yml でコンテナを管理する方法
|方法| 説明|
|----|-----|
|既存のイメージのままコンテナを作成する| DockerHub からイメージを取得し、そのイメージからコンテナを作成する|
|Dockerfile を利用する方法| 既存のイメージを Dockerfile 内の記述でコンテナに合わせてカスタマイズする方法|
<br>

## docker-compose 活用フロー

1. 作業ディレクトリを作成する
2. コンテナの設定ファイルを作成する
3. docker-compose.yml ファイルを作成する
4. docker-compose コマンドを実行してコンテナを起動する

<br>

## docker-compose.yml ファイル

docker-compose ファイルは yml ファイルの記法に則って記述する

```
version: '3'
services:
    db:
        image: postgres
    web:
        build: .
        command: bundle exec rails s -p 3000 -b '0.0.0.0'
        volumes:
             - .:/myapp
        ports:
            - "3000:3000"
        depends_on:
            - db
```

## yml ファイルとは

html などと同じくマークアップ言語の一種。  
構造化されたデータを表現するためのフォーマット  
<br>

### ハッシュ記法

ハッシュ　＝　キーと値を紐づけてデータを構造化する記法  
コロン「:」を活用して、キー:値とすることでデータを紐づける。

```
key01：value01
key02：value02
key03：value03
```

<br>
インデントを開けることで、入れ子（ネスト）にすることが出来る。  
```
services:
    db:
        ネストされた情報
    web:
        ネストされた情報
```
<br>

### 配列の記法

「-」は配列を表している

```
volumes:
    - .:/myapp
ports:
    - "3000:3000"
depends_on:
    - db
```

| キー       | 値            |
| ---------- | ------------- |
| volumes    | [.:/myapp]    |
| ports      | ["3000:3000"] |
| depends_on | [db]          |

<br>

## docker-compose.yml ファイルの用語

- version  
  docker-compose で使用するバージョンを定義している  
  バージョンによって書き方が異なる場合もある  
  <br>

- service  
  アプリケーションを動かすための各要素  
  サービス名は任意で設定できる  
  <br>

  例）
  |サービス名| 説明|
  |---------|-----|
  |php |php のイメージを活用したい時|
  |nginx| nginx のイメージを活用したい時|
  |mysql |mysql のイメージを活用したい時|

```
version:
services:
    サービス名:
        サービスの設定
    サービス名:
        サービスの設定
```

<br>

## 各サービスの設定項目

サービス名にネストしてサービスの設定をしている  
<br>
|項目| 説明|
|---|------|
|image |作成するイメージを指定する|
|container_name |コンテナ名を指定する|
|restart |実行時に再起動する設定|
|environment| 環境変数設定(パスワードなど)|
|ports |DockerImage を立ち上げる際のポート番号の設定|
|volumes| マウントする設定ファイルのパスを指定(mysql の conf など)|
|build |ComposeFile を実行し、ビルドされるときの path|
|depends_on| Service 同士の依存関係の設定|
|entrypoint |デフォルトの entrypoint を上書きする|
|driver| ボリュームに使用するドライバ(動かすための接続先)の指定|
<br>

```
version:
services:
    サービス名:
        image: イメージ名
        ports: ポート番号
        depends_on: 依存関係の設定
    サービス名:
        image: イメージ名
        container_name: コンテナ名
```

<br><br>

# Docker-compose のコマンド

docker-compose.yml ファイルがあるディレクトリで実行  
<br>
|コマンド| 説明|
|-------|------|
|docker-compose up| サービスの image をビルド&起動|
|docker-compose build |サービスの image をビルド|
|docker-compose restart| サービスの再起動|
|docker-compose run |サービスの起動|
|docker-compose exec| サービスに対してコマンド実行|
|docker-compose logs |サービスのログを出力|
|docker-compose stop| サービスを停止させる|
|docker-compose kill |サービスのプロセスを終了する|
<br>

## コンテナのビルド＆起動

複数コンテナを一度にビルド＆起動することが可能  
`$ docker-compose up -d [サービス名]`  
-d オプションにより、バックグラウンドでコンテナを起動することで  
コマンドラインでの作業を継続できる。  
サービス名は、一部のサービスのみ起動したい時に指定する。  
<br>

### ビルドのみ

`$ docker-compose build [サービス名]`
<br>

### 再起動

`$ docker-compose restart`  
<br>

## コンテナ内でコマンドを入力する

単体のコンテナを起動してコマンド実行する場合  
`$ docker-compose run サービス名 実行したいコマンド`  
<br>

### 起動中のコンテナでコマンドを入力する

`$ docker-compose exec サービス名 実行したいコマンド`  
`$ docker-compose exec php bash`  
bash コマンドを実行すると、コンテナ内で exit コマンドを入力するまで、  
コマンドラインが使えるようになる。  
<br>

## ログを確認する

`$ docker-compose logs [サービス名]`  
サービス名を指定すると、単体のログも確認することが出来る。  
<br>

## コンテナを停止させる

`$ docker-compose stop [サービス名]`  
サービス名を指定すると、サービス単体を停止することもできる。  
<br>

### 強制的に終了させたい場合

`$ docker-compose kill [サービス名]`  
サービス名を指定すると、サービス単体を強制終了させることもできる。

<br><br>

# Docker-compose 演習

## 作業ディレクトリを作成

```
docker-compose-practice
├── docker
│   ├── nginx
│   │   └── default.conf
│   └── php
│       ├── Dockerfile
│       └── php.ini
├── docker-compose.yml
└── src
    └── index.php
```

Docker を活用する時は、上記のようなディレクトリ構成で作成することが多い  
<br>
各ディレクトリの役割
|ディレクトリ| 説明|
|-----------|-----|
|docker|dockerfile や conf ファイルなどの作成するイメージの設定ファイルを格納する|
|src| 作成するアプリケーションなどのプログラムコードを格納する|
|nginx |nginx の設定ファイルを格納する|
|php| php をインストールするための設定ファイルや Dockerfile を格納する|
<br>

## コンテナの設定ファイルを作成する

- PHP コンテナ　＝＞　 PHP のイメージを保管するコンテナ
- nginx コンテナ　＝＞　 PHP を動作させるサーバ nginx のコンテナ

### nginx コンテナの設定ファイルを作成

nginx ディレクトリに nginx がデフォルトで用意している設定ファイル  
default.conf を作成することで、ローカルサーバの設定が出来る。  
default.conf に以下を記述

```
server {
    listen 80; # リクエストを受けるサーバーのポート番号
    server_name _; # リクエスト先のサーバのホスト名
    root /var/www; # ドキュメントルートの指定。ホストがアクセスできるディレクトリやファイルの一番上の階層
    index index.php;

    # ログの出力先の指定
    access_log /var/log/nginx/access.log;
    error_log  /var/log/nginx/error.log;

    location / {
        try_files $uri $uri/ /index.php$is_args$args; #URIのパス（リクエスト）に応じたファイルを返す
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```

<br>

### php コンテナの設定ファイルを作成

Docker Hub から PHP のイメージをダウンロードするために  
以下のように Dockerfile に記述

```
FROM php:8.2-fpm

COPY php.ini /usr/local/etc/php/
# php.iniを /usr/local/etc/php/ に作成

RUN apt-get update && apt-get install -y \
    git \
    unzip \
    zip \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
# PHPのパッケージを更新

WORKDIR /var/www
# 作業ディレクトリの指定
```

<br>

php.ini に日本に合わせた時間や文字に関する設定を記述

```
date.timezone = "Asia/Tokyo" #タイムゾーンの設定

[mbstring] #文字に関する設定
mbstring.internal_encoding = "UTF-8"
mbstring.language = "Japanese"
```

<br>

## docker-compose.yml を作成

```
version: '3.8' # docker-composeのバージョンを指定
services:
  nginx: # サービス名
    container_name: "nginx-docker-compose" # コンテナ名
    image: nginx:1.21.1 # 参照するimage名を指定。ここではDockerHub上のnginxイメージを指定している。
    ports:
      - "80:80" # ホストとゲストのポート番号のマッピング。
    volumes:
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf #ディレクトリのバインドマウント。
      - ./src:/var/www/
    depends_on:
      - php # サービス同士の依存関係
  php:
    container_name: "php-docker-compose"
    build: ./docker/php # ビルドする dockerfileの場所。パスを指定する。./docker/php配下のDockerfileを参照して作成
    volumes:
      - ./src:/var/www/
```

<br>

- version => 3.8 を指定
- services => php と nginx というサービス名で作成

### nginx

| サービスの設定項目 | 説明                                                                   |
| ------------------ | ---------------------------------------------------------------------- |
| container_name     | nginx-docker-compose                                                   |
| image              | DockerHub の公式イメージ nginx:1.21.1                                  |
| ports              | ローカルサーバのポート番号、"80:80"                                    |
| volumes[1]         | 設定ファイル（default.conf）をコンテナ内の/etc/nginx/conf.d/にマウント |
| volumes[2]         | src ディレクトリをコンテナ内の/var/www/にマウント                      |
| depends_on         | php から起動されてから、 nginx のサービスが起動する設定                |

<br>

### php

|サービスの設定項目| 説明|
|container_name |php-docker-compose|
|image| Dockerfile を作成しているのでここでは指定していません。|
|volumes |src ディレクトリをコンテナ内の/var/www/にマウント|
|build| ./docker/php ディレクトリにある Dockerfile をビルドする設定|
<br>

## docker-compose コマンドを実行してコンテナを起動

ビルド＆起動するためのコマンド  
`$ docker-compose up -d --build`  
正しく実行できていると、以下のようなログが出る。

```
Creating network "dockercompose-practice_default" with the default driver
Creating php-docker-compose ... done
Creating nginx-docker-compose ... done
```

二つのコンテナが起動している間は、nginx のサーバは立ち上がり、php も利用できる。  
<br>
※GitHub にこのディレクトリをあげておくと、「docker-compose up」コマンドで  
同じ環境を起動することが出来るようになる。  
<br>

## コンテナの停止

コンテナの停止  
`$ docker-compose stop`  
<br>

php コンテナ内でコマンドを実行  
`$ docker-compose run php bash`  
<br>

コンテナを複数起動しておくと PC が重くなるため、  
使わないコンテナは停止させる。
