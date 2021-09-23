FROM python:3.9-slim
WORKDIR /tmp/mydir

# メタ情報の付与
LABEL maintainer="usermail@hogehoge.com"
LABEL version="1.0"
LABEL description="RUNコマンドを使用します。"

# ファイルのコピー
COPY requirements.txt ${PWD}

# Pythonのパッケージをインストール
RUN pip install -r requirements.txt

# 一時変数の定義
ARG wdir

# 作業ディレクトリを変更
WORKDIR $wdir
