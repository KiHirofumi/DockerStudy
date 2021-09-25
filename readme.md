# 目的：

- Dockerの概要と基本的な使い方を理解
- Visual Studio CodeとDockerを組み合わせた開発手法を理解
- 機械学習やDjango/Flask, JupyterNotebookなどのパターンごとに再現性のあるクリーンな開発環境を構築

## 仮想とコンテナ

- 仮想
  - (画像1)
- コンテナ
  - (画像2)
  - 仮想ハードウェアを介さず動作するため軽快
  - 移動や複写が可能
    - コンテナの構成や依存関係はDockerfileと呼ばれる短いテキストファイルで管理される
    - コンテナの起動イメージを保存したImageファイルも大きくて数百MB程度で、インターネット経由で配信の仕組みも用意されている
  - 高速起動

## 開発環境全体イメージ

- イメージ
  - (画像3)
  1. VS Codeからプロジェクトフォルダ開く
  1. Dockerコンテナが起動
     - プロジェクトのソースコードが格納されたフォルダがDockerのコンテナにマウントされる
  1. VSCodeとコンテナが接続される
     - ローカルPCで開発しているのと同じようにプログラミングやデバッグが可能
  1. ソースコードをGithubなどのリポジトリに格納
  1. 他の開発メンバがリポジトリからプロジェクト取得

## 準備

1. CPUの仮想化支援機能の有効化
2. WSL2のインストール
3. Docker Desktopのインストール
   - DockerHubのサインアップもする
4. VSCodeのインストール（してれば不要）
   - 拡張機能「Japanese」は必須
5. GitHubのサインアップ（してれば不要）
6. Gitのインストールと環境設定（してれば不要）

## Docker入門

- 概要
  - (画像4)
  - Dockerイメージ（コンテナのひな型）
    - Linuxディストリビューション(Debian, Ubuntu)
    - アプリケーション(Python)
    - ライブラリやフレームワーク(Numpy, Django)
    - プログラムや設定ファイル(Pythonのソース)
    - コンテナ実行時に起動するプロセス(DjangoのWebサーバーを起動する)
  - Dockerfile
    - コマンドのオプションでも可
    - Dockerfileからイメージを作成することを「ビルドする」という
  - イメージレジストリ Image Registry
    - Dockerイメージを共有する場所
      - DockerHub[https://hub.docker.com](https://hub.docker.com)など
        - Amazon ECRというものもある
        - なるべく「OFFICIAL IMAGE」や「VERIFIED PUBLISHER」と表示されているものを選ぶように
        - タグで多数存在する
          - 開発環境や本番環境としてきちんとDockerfileを作成して継続的に利用するなら「3.9-slim」というようなものにするとよい
          - 初心者は「3.9」などがいい。容量多いが、ひととおりそろっていて、Dockerfileで細かくしていしなくてよい
  - コンテナ
    - イメージを実体化したもの

## Dockerイメージに関するコマンド(docker 1.13バージョン)

|コマンド |説明|
|:---|:---|
|docker image ls|イメージ一覧を表示|
|docker image rm|イメージを削除|
|docker image prune|不要なイメージを一括削除|
|docker image pull|DockerHubからイメージを取得|
|docker image push|DockerHubへイメージを登録|
|docker image build|Dockerfileからイメージをビルド|
|docker image inspect|イメージの詳細を表示|

- [http://docs.docker.jp/engine/reference/commandline/index.html](http://docs.docker.jp/engine/reference/commandline/index.html)

## コンテナに関するコマンド

|コマンド|説明|
|:----|:----|
|docker container ls|コンテナの一覧を表示|
|docker container run|コンテナの作成と起動
|docker container logs|コンテナのログを取得|
|docker container inspect|コンテナの詳細情報を取得|
|docker container stats|コンテナのリソースの使用状況を表示|
|docker  container stop|コンテナの停止|
|docker container create|コンテナの作成(停止状態)|
|docker container start|コンテナの起動|
|docker container restart|コンテナの再起動|
|docker container attach| コンテナに接続(exitでシェルを抜けると停止)<br>停止させたくない場合はCtrl + P -> Ctrl + Q |
|docker container exec|コンテナに接続(exitでシェルを抜けても停止しない)|
|docker container rm|コンテナを削除|
|docker container prune|停止中のコンテナを一括削除|

## runコマンドの例１：コンテナを対話型モードで起動する

- > docker container run -it --rm python:3.9-slim /bin/bash
  - 「Python:3.9-slim」イメージでコンテナを作成
  - コンテナ起動時にシェル「/bin/bash」を実行
  - シェルをexit または Ctrl + C で抜けるとコンテナも停止する
  - -it：「i」は標準入力を受け付ける、「t」は疑似TTY端末を割り当てる
    - 「/bin/bash」と組み合わせて指定することで、コンテナを対話型モードで起動する
  - --rm：コンテナ終了時にコンテナの削除も行う
    - ちょっとした動作確認時に有用！
- > docker container run -it --rm python:3.9-slim python
  - シェルではなく、Pythonを起動する

## runコマンドの例２：コンテナをバックグラウンドモードで起動

- > docker container run --name prj1_websv -d -p 8080:80 nginx
  - nginxはwebサーバー
    - nginxイメージでコンテナを作成
    - バックグラウンド実行
    - 停止はstopコマンド
  - -name：人が見てわかるようにつける名前。今回は「プロジェクト名_アプリケーション名」
  - -d：バックグラウンド実行。シェルを閉じてもかまわない。
  - -p：ホスト側(WindowsやMac)のポートをコンテナ側のポートに割り当てる。
    - nginxが実行されているコンテナポート80をホスト側のポート8080に割り当てている。
- [http://localhost:8080/](http://localhost:8080/)
  - (画像5)
  - ブラウザでアクセスしてみた
- > docker container ls -a
  - STATUS欄で起動時間も確認できる
- > docker container stats prj1_websv
  - コンテナが使用しているリソースをリアルタイム表示
  - 終了時はCtrl + C でOK
- > docker container stop prj1_websv
  - コンテナを停止する
  - docker container ls -a　で確認してみたり
  - docekr container stats で確認してみたり
  - docker container restart prj1_websv で再起動してみたり
- コンテナへ接続(execにするとexitで抜けても停止しない)
  - > docker container exec -it prj1_websv /bin/bash
- 削除するときは停止してからrm
  - > docker container stop prj1_websv
  - > docker container rm prj1_websv

## フォルダのマウント

- コンテナの中でフォルダやファイルを作成しても。コンテナを削除するとともに消えてしまう
- →ホスト側（WindowsやMac）のフォルダをコンテナにマウントする！
- マウントには三種類ある
  - volume
    - ホスト側で名前が付いたボリュームを作成し、コンテナに割り当てる
    - ホスト側から操作しない
    - アクセス速度が早い
    - 公式推奨
  - bind
    - ホスト側の任意のフォルダをコンテナに割り当てる
    - ホスト側でもフォルダの内容を操作できる
  - tmpfs
    - ホストPCのメモリ領域を一時的なフォルダとしてコンテナに割り当て
    - コンテナ終了時に内容も失われる
    - （WSL2の　/mnt/wsl/docker-desktop-data/data/docker/volumes　配下に作成される）
- ということで、volumeのコマンド

|コマンド|説明|
|:----|:----|
|docker volume create|ボリュームの作成|
|docker volume ls|ボリュームの一覧を表示|
|docker volume rm|ボリュームの削除|
|docker volume pune|未使用のボリュームの一括削除|

### volume を使ってみる

- > docker container run -it --rm --mount src=voltest,dst=/tmp/voltest python:3.9-slim /bin/bash
  - ボリューム「voltest」をコンテナの「/tmp/voltest」ディレクトリに割り当て
  - srcにvolume名
  - dstに割当先のディレクトリ名
- > echo "Hello! Docker!" > /tmp/voltest/sample.txt
  - これで/tmp/voltest/sample.txtに「Hello! Docter!」とはいったファイルを作成した
  - > cat /tmp/voltest/sample.txt
  - 打てばわかる。
- > exit
  - docker から抜けた
- > docker volume ls
  - dockerのマウント先を見たらvoltestが存在した
- > docker volume rm voltest
  - 試しに作ったボリュームを削除

### bind を使ってみる

- ホスト側でfolderを作る
  - > mkdir test
- > docker container run -it --rm --mount type=bind,src=$(pwd)/test,dst=/tmp/test python:3.9-slim /bin/bash
  - folder「test」をコンテナの「/tmp/voltest」ディレクトリに割当
  - srcにfolder名
    - ※絶対パスで記載する必要あり！
    - 今回はpwdコマンドで代用（これもよく利用するテクニック）
    - folderが存在しないとエラーになる！
  - dstに割当先のディレクトリ名
  - (画像)
  - Dockerとファイル共有されたよ、という警告。
    - Don't show againで問題ない
- > echo "I'm fine!" > /tmp/test/sample2.txt
  - これで/tmp/test配下にsample2.txtできてる
  - Windows側でも、できているのが見られる
- ホスト側でフォルダごと削除する
  - >rm -R test

## Dockerfile

- Dockerfileを使用すると、DockerHubから取得したベースイメージに手を加えた独自のイメージを容易に構築することができる。

### Dockerfileの記述

|hoge|説明|
|:--|:--|
|FROM|ベースイメージの指定|
|LABEL|情報（メタデータ）の付与|
|RUN|ビルド(build)時に実行するコマンド|
|EXPOSE|コンテナを外部公開する際のポート番号|
|COPY|ファイルやフォルダをコンテナにコピー|
|ADD|ファイルやフォルダをコンテナにコピー（zipの回答や外部URLからコピーする場合）|
|CMD|コンテナを起動(run)するときに１回だけ実行するコマンド|
|ENTRYPOINT|runコマンドで実行するコマンド|
|ARG|一時変数|
|ENV|環境変数|
|USER|ユーザーの切り替え|
|WORKDIR|作業folderの指定|

## Dockerfileによるイメージ構築の概念

- (画像)
- DockerfileはFROMで指定したベースとなるイメージに、それ以降で記述しているコマンド内容を「イメージレイヤ」として重ね合わせることで、新たなイメージを作成していく。
  - イメージレイヤは親子関係を持てる

### 拡張機能「Docker」

- VSCodeの拡張機能「Docker」インストール
  - Dockerfileの作成や編集時のコード補完
  - Docker Composeファイルを編集する支援機能やコード補完
  - コマンドパレットからのDockerコマンド入力
  - Docker Explorer
    - 拡張機能クリックしたときに表示されるやつ
    - (画像)
- Dockerfileの作成想定
  - ホームディレクトリ直下に「Dockerenv」folderを作成
  - dockerenv配下にプロジェクトごとのフォルダ作成
  - プロジェクトのフォルダ(ex1)をVSCodeで開いて作業を行う
  - プロジェクトごとのフォルダやファイルの構成は記述例の冒頭に記載する
  - こんな感じ
    - ホームディレクトリ/
      - dockerenv/
        - ex01/ #プロジェクトのフォルダ
          - Dockerfile #プロジェクトに含まれるファイル
