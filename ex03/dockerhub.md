# DockerHubへの登録

GitでDockerfileを管理する
ベースイメージの取得元をDockerHubとする

1. DockerHubでリポジトリを作成
   - [https://hub.docker.com/repositories](https://hub.docker.com/repositories)にアクセスし、「Create Repository」ボタンをクリック
1. リポジトリ名を入力（「ex03」）
   - 公開リポジトリと非公開リポジトリを選択できるが、非公開は二つ目以降有償となる
1. VSCodeのターミナルでイメージ一覧を確認
   > docker image ls
   - 「ex03/nginx」が表示される
1. DockerHubのリポジトリ名にあわせて、新たなタグをつけたイメージを作成する
   > ex) docker tag 元のイメージ名:タグ DockerHubのアカウント名/リポジトリ名:タグ
   > docker tag ex03/nginx:1.0 アカウント名/ex03:1.0
   >docker image ls
   - ex03/nginxと同じIMAGE IDで「アカウント名/ex03」が表示されていればOK
1. DockerHubに登録
   > docker push アカウント名/ex03:1.0
   - DockerHubに「Last pushed:xx minutes ago」が表示されているはず
1. DockerHubに登録したイメージでコンテナ起動する
   1. いったんローカルのイメージ削除する
      > docker image rm アカウント名/ex03:1.0
      > dockerimage rm ex03/ntinx:1.0
      > docker image ls
      - 削除されていること
   1. DockerHubのコンテナを起動
      > docker run --name ex03_websv2 -d --rm -p 8080:80 アカウント名/ex03:1.0
      - [http://localhost:8080](http://localhost:8080)にアクセスしてWebSvが起動していることを確認
   1. コンテナを停止する
      >docker container stop ex03_websv2
