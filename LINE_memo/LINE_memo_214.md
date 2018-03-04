LINEメモ
# 2/14 rails
```
gem install rails -v 5.1.1
rails new 'app_name' -d mysql
```
application.rbに追加
```
config.time_zone = 'Tokyo' 
config.active_record.default_timezone = :local
```

Cloud9でサーバを建てる時
```
rails s -b $IP -p $PORT
```

---

本ではhello_controller.rbにindexメソッドを書いたが、hello/以下にindex.html.erbを置くと同じ結果になり、これが普通。

controllerに関数を定義することと、ディレクトリに関数名.html.erbを置いて記述することが同じ。
```
rails generate model 'model_name' {変数定義}
```
参考書ではmodel_nameはbookとしていた。
これを

```
rails db:migrate
```
でDBを作成('model_name.yml'が生成)
この.ymlファイルにデータを記述すると
```
rails db:fixtures:load FIXTURES=books
```
で読み込める。

```
rails dbconsole
```
で`config/database.yml`に記述した設定でDBに接続できるので、`db:migrate`や`db:fixtures:load`の後に確認するとよい。

modelに関するファイルを全て削除するコマンド
```
rails destroy model book
```
DBを削除
```
rails db:drop DISABLE_DATABASE_ENVIRONMENT_CHECK=1
```

---

# Scaffolding機能を利用
```
rails db:migrate
```
は手動でやる必要がある

### new/create アクションメソッド
```
def new
	@book=Book.new
end
```
空のbookモデルを一つ作る。フォームの項目を正しく生成するために行う処理。
```
def create
	@book=Book.new(book_params)
end

def book_params
	params.require(:book).permit(:isbn,:title,:prce,:publish,:published,:dl)
end
```

book_paramsアクションメソッドにより、フォームの入力がハッシュ形式で取得できる。
それを@Book.new()の引数とすることでそれらのプロパティを持つモデルが生成できる。
これを、`@book.save`によりDBに登録できる。

## GETとPOST
- GET
URLを入力して結果を取得する。ログ履歴を見ると検索ワードなどがバレる。
IDやPASSWORDの内容がログから盗聴される。
- POST
入力フォームなどから新たな入力を送信する。
body部分に入力内容が記述されるのでログ履歴からはバレない。
httpsはbodyの内容をさらに暗号化しているので他者からは見れない。

## コマンドからrailsをいじって変数などを確認する
```
rails console
```
で対話式に確認できる。
```
rails console -s
```
とすると、コンソール終了時にデータベースへの変更を自動でロールバックしてくれる。