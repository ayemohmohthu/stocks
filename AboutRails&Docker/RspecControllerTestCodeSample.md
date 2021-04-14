---
title: RSpec コントローラのテストの書き方まとめ
tags: Ruby Rails RSpec
author: geshi
slide: false
---


# はじめに
RSpecを用いたコントローラーのテストは、
テストケース設計　> テストデータ作成 > テストロジック作成 > リファクタリング
という順番で書きます。
この記事では、プロセス順にそれぞれの要点を述べますが、
特に、テストケース設計、テストデータ作成のコツ、よく使うリファクタリングについて重きを置きます。
以上の3つができるようになれば、テストに対する苦手意識はなくなるでしょう。
また、テストデータ作成はFactoryGirlを用いるが、最低限の使い方はここでは割愛します。

# 本文
## １．テストケース設計

### ◯書くべきテストケース
コントローラー書くべきテストケースは基本的に5項目です。

1. 正常に動作しているか (http status)
2. 正常にHTTPメソッドを呼び出せているか (render template)
3. インスタンス変数が適切かどうか (assings)
4. モデルの増減 (change by)
5. リダイレクト (redirect)

4,5はコントローラ内に存在しない場合もある。
例えば、モデルの増減はDBにレコードがsave、update、destroyされた時に限る。

***
### ◯条件分岐に沿って設計
書くべきテストケースは5つであるが、
条件分岐を考える必要があります。
例えば、

```books_controller.rb

def show

	@book = Book.find(params[:id])
	
	if @book.author.present?
		@author = book.authors.first
	else
		@author = Author.new(book_id: @book.id)
	end
end	

```

といったメソッドがあるとします。
その場合は、
@book.authorが存在する時と、存在しない時のインスタンス変数をテストする必要があります。

***
### ◯describe/context/itの使い分け
①describe : テスト対象が何か
②context  : 条件は何か (with or whenから始める)
③it       : アウトプットは何か 

#### ※具体例

```books_controller_spec.rb
describe "GET show" do
	context "when @book.author present" do
		it "render assings author to @author"
	end
end
```

***
### ◯まとめ
条件分岐に従って、5つのテストケースをdescribe/context/itを用いて設計します。


## ２．テストデータ作成
### ◯基本

```books_controller_spec.rb
@author = create(:author)
@book   = create(:book, author: @author)
```

のように、テストデータを作成して欲しいのですが、
コントローラーのメソッドが複雑であればあるほど、テストデータは複雑になります。
難しくなる原因は、アソシエーションと、データの構造(hash/array)です。
例えば、以下のメソッドを考えてみます。


```books_controller.rb

def show
	@book   = Book.find(pamras[:id])
	@author = @book.author
	@sales  = @book.sales_amount #インスタンス変数(本の売上金額を計算)
	
	if @sales > 1000000
		@book_rank = @book.rank_in(@sales)　#インスタンス変数(本をランキングに載せる(boolean))
	else
		@book_rank = @book.rank_out(@sales) #インスタンス変数(本をランキングに載せない(boolean))
	end
	
	@author_rank = @author.rank(@book_rank)
end

``` 
***

```book.rb

def sales_amount
		~~~~
		~~~~
		~~~~
	sales
end

```


books_controllerのshowメソッドは結構複雑です。
showメソッドの中に条件分岐がありますし、
salse_amount/rank_in/rank_outといったインスタンスメソッドがあります。
複雑だと、一発で正しいテストデータを作成出来ません。
コントローラ内の、どこまで動いて、どこで止まっているかを突き止める事が大切です。
そこで、以下の下準備をしておくことをオススメします。

***

### ◯テストの下準備　[重要]
1. インスタンス変数、ローカル変数、パラメータ全てに p @~~~ で出力。(モデルのメソッド内の変数も)
2. メソッドの始まり際に p "メソッド名 Before"/ 終わりに p "メソッド名 After"とマークを付けておく
3. 条件分岐ごとに、p 'sales 100万円以上' のようにマークを付けておく。
3. dev環境で一度出力結果を取得しておく。


これらの4つの下準備をやっておくだけで、思考停止することも、ぐるぐるすることもなくなります。
どのメソッドが正常に動いていないかがすぐ分かり、初心者のテストデータ作成の効率が大幅に上がります。


↓　例


```books_controller.rb

def show
	@book   = Book.find(pamras[:id])
	p @book
	@author = @book.author
	p @author
	@sales  = @book.sales_amount #インスタンス変数(本の売上金額を計算)
	p @salse
	
	if @sales > 1000000
		p '売り上げ100万以上'
		@book_rank = @book.rank_in(@sales)　#インスタンス変数(本をランキングに載せる(boolean))
	else
		p '売り上げ100万以下'
		@book_rank = @book.rank_out(@sales) #インスタンス変数(本をランキングに載せない(boolean))
	end
	
	p @book_ranks
	@author_rank = @author.rank(@book_rank)
	p @author_rank
end

``` 
***

```book.rb

def sales_amount
	p 'salse_amount Before'
		~~~~
		~~~~
		~~~~
	p salse
	p 'sales_amount After'
end

```

***

## ３．テストロジックの書き方

### ◯使うロジック

 1. expect(response.status).to eq(200)
 1. expect(response).to render_template template 
 1. expect(assings(:book)).to eq @book
 1. expect{ post :create, id: @book.id }.to change(Book, :count).by(1)
 1. expect(response).to redirect_to author_path

です。
上から、

1. 正常に動作しているか (http status)
2. 正常にHTTPメソッドを呼び出せているか (render template)
3. インスタンス変数が適切かどうか (assings)
4. モデルの増減 (change by)
5. リダイレクト (redirect)

をテストしています。
もちろん、3番はpostの時意外にも、delete/updateの時もあるので、適宜変えて下さい。


## ４．リファクタリング
リファクタリングができると、テストが楽しくなります。
初心者を脱するための、tipsを取り上げます。　
順番はよく使う順に並べています。☆は難易度です。

### ◯before ☆

before

```books_controller_spec.rb

it 'test1' do 
	@book = create(:book)
	get: show, id: @book.id
	~~~~
end

it 'test2' do 
	@book = create(:book)
	get: show, id: @book.id
	~~~~
end

```

after

```books_controller_spec.rb

before:each do 
	@book = create(:book)
	get: show, id: @book.id
end

it 'test' do
	~~~
end
	
it 'test2' do
	~~~
end	


```

＊解説
同じ処理をまとめることが出来る。

***
### ◯let/let! ☆☆

before

```books_controller_spec.rb


describe "A " do
	it 'a test' do
		@book = create(:book)
		~~~
	end
	
	it 'b test' do
		~~~
	end

	it 'c test' do
		@book = create(:book)
		~~~
	end	
end
```

after

```books_controller_spec.rb

describe "A " do
let(:book){create(:book)}
	it 'a test' do
		book
		~~~
	end
	
	it 'b test' do
		~~~
	end

	it 'c test' do
		book
		~~~
	end	
end
```

＊解説
letは遅延評価といって、呼び出さないと実行されません。
let!だと呼び出さなくても実行されます。
変数を定義するときは、letを用いたほうが、圧倒的にdryに書けます。

***
### ◯shared_example ☆☆☆
テストのロジックは単純です。だからこそテスト内に、同じロジックが重複します。
そこで登場するのがshared_exampleです。

before

```books_controller_spec.rb

describe 'A' do
	before { get :show, id:@book.id }
	it { expect(response.status).to eq(200) }
	it { expect(response).to render_template show }
	it { expect(assings(:book)).to eq @book }
end

describe 'B' do
	before { get :book_rank, id:@book.id }
	it { expect(response.status).to eq(200) }
	it { expect(response).to render_template book_rank }
	it { expect(assings(:book)).to eq @book }
end

```

after

```books_controller_spec.rb

shared_examples_for "3tests" do |template|
	it { expect(response.status).to eq(200) }
	it { expect(response).to render_template template }
	it { expect(assings(:book)).to eq @book }
end


describe 'A' do
	before { get :show, id:@book.id }
	it_behaves_like "3tests" ,:show
end

describe 'B' do
	before { get :book_rank, id:@book.id }
	it_behaves_like "3tests" ,:book_rank
end


```
＊解説
２つ以上同じメソッドがあるときは、shared_exampleを使った方が綺麗になります。
share_examples_for "名前"　do |引数|
とすることが出来ます。

***
### ◯factory dataのassociation ☆☆☆
1 author ←　many books の関係です。

belongs_to

```factories/books.rb
factory :book do
	author
	title "ノルウェイの森"
end


factory :author do
	name "村上春樹"
end

```


has_many

```factories/authors.rb
factroy :author do
	name "村上春樹"　
	
	after(:create) do |author|
		3.times do
			create(:book, author: author)
		end
	end
end
```

＊解説
これで、create(:author)と宣言すれば、
authorに紐付いた、3つのbooksのテストデータも作成されます。
今まで、7行で書いていたテストデータが、2行になります。

before

```books_controller_spec.rb
@author = create(:author)
@book = []
i = 0
3.times do 
	@book[i] = create(:book, author: @author)
	i++
end
```

after

```books_controller_spec.rb
@author = create(:author)
@book = @author.books
```


***
### ◯まとめ
理論より、具体的なコードを載せました。
仕組みがわからないところは、検索すれば一つ一つ詳しい解説が載っているので照らしあわせて下さい。


***
#　結論
テストケース設計と、テストデータ作成、リファクタリングができれば、RSpecの苦手意識はなくなります。
今回の記事で一番重要な情報は、テストの下準備です。初心者の方はまずデバッグの方法を学んでテストデータ作成に慣れて下さい。




