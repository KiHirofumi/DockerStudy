# Dockerfileの記述例2：RUNコマンドの使用

## プロジェクトの構成

- ex02/
  - src/
    - sample1.py
  - Dockerfile
  - requirements.txt

## Dockerfile

```Dockerfile
FROM python:3.9-slim
# 作業ディレクトリを変更
WORKDIR /tmp/mydir
# ファイルのコピー
COPY requirements.txt ${PWD}
# pythonのパッケージをインストール
RUN pip install -r requirements.txt
# 一時変数の定義
ARG wdir
# 作業ディレクトリを変更
WORKDIR $wdir
```

- COPY
  - ホストからファイルをコピーする
  - ADDは多機能なため、コピーだけならCOPYを使うように
- RUN
  - docker image build を行う際に実行するコマンド
    - アプリケーションのインストール
    - モジュールのインストール
    - ユーザ追加など
- ARG
  - Dockerfile内でのみ使える一時変数を定義
    - Dockerfileの中では「$wdir」のようにして参照できる

## requirements.txt

```requirements.txt
numpy == 1.20.0
matplotlib == 3.4.0
```

- pipでPythonのモジュールをインストールを行う際に参照するファイル

## sample1.py

```sample1.py
import matplotlib.pyplot as plt
import numpy as np

# x軸：時刻
x = np.arange(0,100,0.5)

# y軸：sin波
Hz = 5.
y = np.sin(2.0 * np.pi * (x * Hz)/100)

# グラフを描画
plt.plot(x,y)
plt.savefig('test.png')
```

- グラフを描画するサンプルコード
- イメージのビルド
  - > docker image build --build-arg wdir=/tmp/src -t ex02/python:1.0 .
    - 最後の「.」忘れずに！
- コンテナの起動
  - > docker container run -it --rm --mount type=bind,src=$(pwd)/src,dst=/tmp/src ex02/python:1.0 /bin/bash
    - > ls
    - > python sample1.py
    - > exit
