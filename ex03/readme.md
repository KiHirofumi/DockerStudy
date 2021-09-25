# Dockerfileの記述例3：CMDコマンドの使用

## コンテナ起動時にコマンドを実行する

- Webサーバーなどを稼働させたい場合
- 今回Webサーバーはnginx

## プロジェクトの構成

- ex03/
  - src/
    - index.html
  - Dockerfile

## Dockerfile

```Dockerfile
FROM ubuntu:20.04
# Nginxのインストール
RUN apt-get update && apt-get install -y \
    nginx \
    tzdata \
 && rm -rf /var/lib/apt/lists/*
# タイムゾーンを設定する環境変数
ENV TZ=Asia/Tokyo
# ポートの指定
EXPOSE 80
# ファイルのコピー
COPY src/index.html /var/www/html/
# Nginxの起動
CMD ["nginx", "-g", "daemon off;"]
```

- RUN apt-get
  - aptではなく、コマンドライン用のapt-get
    - apt-get upgradeは行わない。ベースイメージのパッケージ更新はベースイメージの管理者が行うべき事項であり、Dockerfileで行うことは必要なパッケージのinstallのみ。
  - rm -rf ... ここはパッケージ管理システムのキャッシュをクリアしている。
    - イメージサイズを小さくしてビルド時間も短くするため
  - apt-getで「Releasse file for xxxx is not valid yet」のようなエラーのときには、WSL2の時刻がずれている可能性がある
    - WSL2(Ubuntu)の起動→「sudo hwclock -s」を実行すること
- ENV
  - コンテナの中でも使用できる環境変数を定義
- EXPOSE
  - コンテナのアプリケーションで使用するポート番号を指定。
  - 指定したポート番号はコンテナを起動する際「-p」オプションでホストのポートに割り当てられる
    - ex. 「docker run -d -p 8080:80 nginx」
    - これにより、外部からは[「https://localhost:8080/index.html」](https://localhost:8080.index.html)のようにポート番号8080でWebサーバーにアクセスできるようになる。
- CMD
  - docker run の際に実行するコマンド
    - CMDは1つしか書けない。（複数のばあいは最後のCMDが実行される）
    - 実行するコマンドと引数をカンマで区切って記述
    - シェルでは「nginx -g daemon off」とするのと同等
    - コンテナでWebサーバーなど実行する場合、バックグラウンドで実行するとコンテナがすぐに停止してしまう
      - nginxのグローバル設定オプション「-g」で「daemon off」として、フォアグラウンドで起動する

## index.html：現在日時を表示する

```index.html
<!DOCTYPE html> 
<html lang="ja"> 
    <head> 
        <meta charset="UTF-8"> 
        <meta name="viewport" content="width=device-width, in initial-scale=1.0"> 
        <title>Sample</title> 
    </head> 
    <body> 
        <h1>こんにちは！Docker！</h1> 
        <p id="msg"></p> 
        <script language="javascript" type="text/javascript"> 
            const msg = document.getElementById("msg"); 
            var now = new Date(); 
            var y = now.getFullYear(); 
            var m = now.getMonth() + 1; 
            var d = now.getDate(); 
            var h = now.getHours(); 
            var mi= now.getMinutes(); 
            var s = now.getSeconds(); 
            msg.innerHTML = `ただ今の日時は${y}/${m}/${d} ${h}:${mi}:${s} です！`; //バッククォートだよ。シングルコーテーションじゃないよ！
        </script> 
    </body> 
</html>
```

## イメージのビルドと動作確認

- > docker image build -t ex03/nginx:1.0 .
- > docker run --name ex03_websv -d --rm -p 8080:80 ex03/nginx:1.0
  - HTMLが表示されるはずだったけど、「正常に接続できませんでした」が表示された。
- > docker container logs ex03_websv
  - ログを確認
  - 誤字・脱字を発見「;」「,」「`」なんか注意ね。
- 表示されました。
