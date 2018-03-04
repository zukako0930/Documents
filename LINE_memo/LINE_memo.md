LINE Schoolメモ
# 2/7
### ホームページにセキュリティ機能をつける．
`var/www/html`にて
```
$ sudo htpasswd -c /etc/httpd/conf/.htpasswd "パスワード"
```
`/etc/httpd/conf/httpd.conf`に以下を追加

```
<Directory "/var/www/html/">
        AuthUserFile /etc/httpd/conf/.htpasswd
        AuthGroupFile /dev/null
        AuthName "Basic Auth"
        AuthType Basic
        Require valid-user
</Directory>
```
`httpd`の再起動
```
$ sudo service httpd restart
```
- HTTPSを使うことでパスワードを盗聴されない．自分で証明書を作って用意できる.
```
# rsa鍵を作る
$ openssl genrsa -out ca.key 2048
# ca.keyからca.csrを作る
$ openssl req -new -key ca.key -out ca.csr
# 色々記入(国とか名前とか)したら証明書を作る
$ openssl x509 -req -days 365 -in ca.csr -signkey ca.key -out ca.crt
# ca.csr ca.key ca.crt(証明書)が生成
$ sudo cp -p ca.crt /etc/pki/tls/certs/
$ sudo cp -p ca.key /etc/pki/tls/private/
$ sudo cp -p ca.csr /etc/pki/tls/private/
$ restorecon -RvF /etc/pki/
```
下記ファイルを生成したファイルを使用するように書き換える
```
$ sudo vim /etc/httpd/conf.d/ssl.conf
```
- 正しく設定したのにアクセスできない!
AWSのサーバ上でインバウンドのポートを追加していなかった．
HTTPS 443ポートを許可する必要がある．ルールに追加．

- 手作業でのデプロイ
ローカルでの作業終了後サーバ側で`git clone`し，以下のようにして`index.html`などを更新
```
$ sudo cp -p ./* /var/www/html/
```

- デプロイの自動化
`.sh`のシェルスクリプトを書く
```deplpy.sh
#!bin/bash

git fetch
git pull
sudo cp -p ./* /var/www/html/
```
実行権限の付与
```
$ sudo chmod 744 deploy.sh 
```
## tips
### vim
- `o`で次の行からインサート
- `shift+g`でファイルの末尾に移動
- `^`で行の先頭，`$`で行の末尾
- `ctrl+v`でモード切り替え→カーソルで範囲指定(`$`で末尾移動など)→`y`(ヤンク)でコピー
- コピーしたら`p`でカーソル位置にペースト
- `dd`で行消し
- `yy`で行コピー
### terminal
- `ctrl+a`で先頭，`ctrl+e`で末尾に
- `sudo yum list installed | grep httpd` 右側は，httpdを含む行だけ返す処理に渡している． 