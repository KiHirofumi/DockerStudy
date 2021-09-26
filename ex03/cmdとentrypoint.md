# Dockerfile に関するその他のトピック

## CMDとENTRYPOINTのちがい

- CMD
  - コンテナ起動時に実行するコマンドを指定
  - docker container run で実行するコマンドを変更できる
  - 記述例

```Dockerfile
 CMD ["python"]
```

- コマンドで実行
  - > docker container run -it --rm python:3.9-slim
    - pythonが実行される
  - > docker container run -it --rm python:3.9-slim /bin/bash
    - /bin/bashが実行される（runで指定したほうが優先）
- ENTRYPOINT
  - コンテナ起動時に実行するプロセスを指定
  - docker container run でプロセス名変更不可
  - 記述例(Dockerfile)
    - [・・・]で囲うとシェルを介さずにコマンド実行

```Dockerfile
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

- ENTRYPOINTとCMDの併用
  - 併用した場合、CMDはENTRYPOINTの引数を指定する役割に代わる
  - 記述例(Dockefile)

```Dockerfile
ENTRYPOINT ["python"]
```

- コマンドで実行
  - >docker container run -it --rm python:3.9-slim sample1.py
    - *python sample1.py*が実行されることになる！

## USERの使用方法

何もしていなければrootユーザーで起動される

- プロセスごとに実行ユーザーを指定することが一般的
  - コンテナでもプロセスを実行するユーザーを指定したい場合に使用
- rootユーザーでファイル作成するとファイル所有者もrootになってしまう
- 記述例(Dockerfile)

```Dockerfile
ENV USER user1
# 一般権限のユーザーを追加
RUN useradd -m ${USER}
# sudo 権限を付与
RUN gpasswd -a ${USER} sudo
# パスワード設定
RUN echo "${USER}:pass" | chpasswd

# ユーザーの切替
USER ${USER}
```
