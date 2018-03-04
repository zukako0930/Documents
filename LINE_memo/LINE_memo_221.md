# LINEメモ221

`Gemfile`に以下を追加
```
gem 'rspec-rails'
gem 'factory_bot'
```
```
$ bundle install
```
`spec/rails_helper.rb`に以下を追加
```
config.include FactoryBot::Syntax::Methods
config.before(:all) do
   FactoryBot.reload
end
```

```
$ rails g rspec:install
$ rspec
```

`pec/factories/books.rb`
にファイルを置いておくとテストを簡単にできる。
`Gemfile`に記述した`facory_bot`がなんとかしてくれる
```
FactoryBot.define do
  factory :book do
    sequence(:isbn)   { |n| "000-0-0000-0000-#{n}" }
    sequence(:title)  { |n| "Mybook no.#{n}" }
    price 1000
    publish "技術評論社"
    published "2015-09-30"
    dl false
  end
end
```

Bookオブジェクトに対するrspecファイルを生成。
```
$ rails g rspec:model Book
```
`spec/models/book_spec.rb`に追加
```
  context "1st model test" do
    it "1 book" do
      book = build(:book) # Book.new -> .saveしない
      # book = create(:book) # Book.new -> .save
      expect(book).to be_valid
    end
  end
```
変更するたびに`rspec`でテストを行える。
`spec/book_spec.rb`を見れば今までクリアしたテストが記述されているため、開発が進めやすい。


```
$ rails g rspec:controller Books
```
によってcontroller用のrspecファイルを生成。

