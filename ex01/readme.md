# Dockerfileの記述例１：シンプルなPython環境

- プロジェクトの構成
  - ex01/
    - Dockerfile
- Dockerfileの内容

```Dockerfile
FROM python:3.9-slim

# メタ情報の付与
LABEL maintainer="usermail@hogehoge.com"
LABEL version="1.0"
LABEL description="シンプルなPythonイメージです。"

# 作業ディレクトリを変更
WORKDIR /tmp/mydir
```

- FOM：ベースとなるイメージ
  - PCにあればそれを使用
  - なければDockerHubから取得(pull)する
- LABEL：メタ情報を付与
  - 作者、バージョン、詳細情報
  - 「docker image inspect イメージ名：タグ」で確認できる
- WORKDIR：作業ディレクトリ
  - なければ作成される
- Dockerfileからイメージをビルド
  - > docker image build -t ex01/python:1.0 .
  - 「-t」オプション：「-t 名前:タグ」でイメージの名前とタグを指定
    - Dockerfileの名前がDockerfileであればパスには「.」を指定
      - そうじゃない場合は「-f Dockerfile名」となる
    - ※ vscodeはデフォルトでUTF-16になってしまっているため、エラーとなった！
      - UTF-8にして保存しなおす
    - ※ ERROR [internal] load metadata for docker.io/library/python3.9-slim:latest
      - DockerExplorerのpython3.9-slimを右クリック→pull してみた
        - 解消せず
      - c:\users\user\.docker\config.jsonの中の「credsStore」の部分を「credStore」にする
        - 解消せず
      - Docker DesktopのアプリからSetting -> Docker Engineにいき、「buildkit」をfalseにしてみる
        - 参考：https://zenn.dev/hiszuk/articles/cb30071df19a1b4f8365
        - 悪化した？のてtrue に戻す
      - もう一度１行目の「FROM python:3.9-slim」を打ち直す
        - 通った！！！なぜ？？ｗｗ
  - > docker container run -it --rm ex01/python:1.0 /bin/bash
    - コンテナ起動してみる
    - > python --version
      - Python 3.9.7であることがわかる
    - > pwd
      - tmp/mydirにいる
    - > exit
      - 抜ける
  - > docker image inspect ex01/python:1.0
    - LABELで指定したメタ情報が表示される
