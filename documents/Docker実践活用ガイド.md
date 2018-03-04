### docker runコマンドは実行するたびに新しいコンテナを作成する．
あるコンテナで作成したファイルは他のコンテナからは確認できない．

### nginxイメージでWebサーバを動かしてみる
`docker run -p 8080:80 nginx`
Dockerコンテナのネットワークに直接外側から接続することができないので，Dockerホストの8080ポートとDockerコンテナの80番ポートを紐づけることで外側から見れるようにする．

### localhost:8080で繋がるはずが，接続できない問題が発生
https://qiita.com/tsukapah/items/3f0660ad7a27a4585bf3
ipv6で解決しないように設定ファイルの該当部分をコメントアウトすると解決するらしいが...
解決しませんでした．

### Dockerイメージとは
コンテナで利用するファイルシステムや実行環境・設定を1つにまとめたもの．
Dockerイメージを使うことでいつでもどこでも同じ状態のコンテナを作れる．

### docker --command
Docker Hub等のレジストリからDockerイメージをダウンロード(docker pull)
ローカルリポジトリのDockerイメージを使ってコンテナを作成する(docker run)
コンテナ内のファイルシステムからDockerイメージを作成する(docker commit)
Dockerfileで記述した構築方法をもとにDockerイメージを自動生成(docker build)
DockerイメージをDocker Hubに保存する(docker push)

### Dockerfile
コンテナから`docker commit`でDockerイメージを作成したが，毎回コマンドを打たないといけないのが辛い．内容も忘れる．
Dokerfileに書いておけば自動で生成してくれる．

```Dockerfile
FROM ubuntu
  
RUN apt-get update
RUN apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_4.x | bash -
RUN apt-get install -y nodejs
```

```
$ docker build -t ubuntu-nodejs-2 .
```
．を忘れると実行できなかった.

```
$ docker run ubuntu-nodejs-2 node --version
v4.8.6
```
### 複数のDocker
- Docker Machine
	- Dockerホストを構成するツール 
- Docker Swarm
	- 複数のDockerを纏めるツール 
- Docker Compose
	- 複数のコンテナを利用するサービスを管理するツール

### Dockerコンテナ全て消す
`$ docker rm $(docker ps -aq)`
### Dockerイメージ全て消す
`$ $ docker rmi $(docker images -q)`

### ホストとコンテナ間のファイルのやりとり
ホスト→コンテナ
`$ docker cp my.cnf <コンテナID>:/etc/my.cnf`
コンテナ→ホスト
`$ docker cp <コンテナID>:/etc/my.cnf my.cnf`

### pyenv周りの設定について
`.bashrc`か`.bash_profile`か`.profile`に以下の記述．
```
export PYENV_ROOT=$HOME/.pyenv
export PATH=$PYENV_ROOT/bin:$PATH
eval "$(pyenv init -)"
```
どれに書けばいいかいまいちわからない.
`.bashrc`に書いておけば`$ source .bashrc`でとりあえずパスは通る．
その後`$ pyenv install 3.5.1`をしようとしたら始まらない...
`curl`とか`wget`とかのアドレスからダウンロードするやつを入れないといけなかった．
`curl`を入れたら無事にinstallできました.

dockerにおける`~`=`root`
`source ~/.bashrc`=`source root/.bashrc`

### pythonのコンパイル時に他言語のパッケージも必要になる 
-ubuntu
```
$ apt-get install git gcc make openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev
```

### Ubuntuに`pyenv install [---]`で色々入れるとエラーになる．
`apt-get install libssl-dev`でpythonは入るようになったが,anacondaが入らない．
anacondaを入れようとすると,
```
tar (child): bzip2: Cannot exec: No such file or directory
tar (child): Error is not recoverable: exiting now
tar: Child returned status 2
tar: Error is not recoverable: exiting now
```
でエラー.やはり何かが足りないっぽい...
というわけで色々ググって適当に入れまくる(すでに入っているやつは無視されるのでOK)．
```
$ apt-get install build-essential libncursesw5-dev libgdbm-dev libc6-dev zlib1g-dev libsqlite3-dev tk-dev libssl-dev openssl libbz2-dev libreadline-dev
```
ようやくanacondaが入りました．

pythonのビルドにもanacondaのビルドにも色々ライブラリ必要なんやな〜って感じ(konami)
#### [今回詰まった流れ]
apt-getで色々入れる→gitからpyenv入れる→pyenv install 3.5.1できない→pythonのビルドのためのライブラリ入れる→pyenv install anaconda3-4.3.1できない→anacondaのビルドのために必要なライブラリ入れる→解決
#### Dockerで作るUbuntu環境は本当に空っぽなので自分でこういうの入れないといけない．





