### Railsについて
#### MVCベース  
```
M-model - データ管理用  
V-view - 画面表示用  
   (html,css,js,...)
　C-controller - backend process実装用
　　(Ruby,...）  
・ViewとControllerの紐つけとして、route.rbでリンクの定義をする必要があること。  
・Model作成した後に作ったモデルデータがDBに反映するため、migrateする必要があること。   
　　rake db:migrate  
・なには特殊な処理をする時は必要なGemをインストールする必要があること。  
　例えば、画像アップロードならmini-magick gem  
・HttpRequestsのPutとPatchの使い分け  
　両方とも編集ようリクエストですが、PUTなら、一部編集したいのにあるリソース全部送信する必要があり、PATCHなら編集したい一部だけのリソースを送信する必要なので、easier&safer.
```
#### scaffold作成
```
rails generate scaffold モデル名 カラム名1:データ型1 カラム名2:データ型 2
例：rails generate scaffold user name:string age:integer
```
#### caffoldで作成したファイルを全削除
```
rails destroy scaffold "name"
```
#### modelをDBに反映
```
rake db:migrate
```
#### modlesで入力チェック
```
class User < ApplicationRecord
  validates :name, presence: true
  validates :phone, presence: true, format: {with: /\A[0-9-]{,14}\z/}
end
```
#### 入力チェック一覧
|入力チェック|説明|
|:--------------------------------|:--------------------------------------------------|
| ':presence => true | 未入力のチェックを行う |
| :length => { :minimum => 2, maximum=> 10 }	| 指定された値の長さをチェックする |
| :format => {:with => /^([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})$/i}	| 書式のチェックを行う |
| :acceptance => true	| 規約同意などのチェックボックスがチェックONにされているかを検証する |
| :confirmation => true	| パスワードなどの確認入力項目が、比較元項目と一致しているかチェックを行う |
| :exclusion => { :in => %w(www us ca jp) }	| 指定された値が含まれないことをチェックする |
| :inclusion => { :in => %w(small medium large) }	| 指定された値が含まれることをチェックする |
| :numericality => { :only_integer => true }	| 入力値が数値であるかチェックする |
| :uniqueness => true	| 入力値がユニークであるかチェックする |

#### Railsでhtml（テンプレート）中にif文を書きたい時
https://qiita.com/devdyaya/items/50d5527983d4c91ae6da

```sample.html.erb
<div class="sample">
  <% if foo == 'bar' %>
    hogehoge
  <% elsif foo == 'piyo' %>
    fugafuga
  <% else %>
    piyopiyo
  <% end %>
</div>
```
#### RubyOnRailsでdropdownlistを使用したい時
```
<%= f.select :desired_attribute, ['option1', 'option2']%>
<%= f.select カラム名, [["表示する文字”, "データベースに登録する値”]] %>
<%= form.select :status, [['未着手', '0'],['作業中', '1'],['完了', '2']] %>
```
#### routes.rbについて
4.3 名前付きヘルパーをオーバーライドする  
:asオプションを使用すると、名前付きルーティングヘルパーを上書きして異なる名前を使用できます。例:
```
resources :photos, as: 'images'

resources :tasks なら photosとimagesのところにtasksを入れてみる。

```
上のルーティングでは、/photosで始まるブラウザからのパスを認識し、このリクエストをPhotosコントローラにルーティングしますが、ヘルパーの命名に:asオプションの値が使用されます。

|HTTP 動詞	| パス	| コントローラ#アクション	| 名前付きヘルパー|
|:----------|:-----|:---------------------|--------------|
|GET	| /photos	| photos#index	| images_path |
|GET	| /photos/new	| photos#new	| new_image_path |
|POST	| /photos	| photos#create	| images_path |
|GET	| /photos/:id	| photos#show	| image_path(:id) |
|GET	| /photos/:id/edit	| photos#edit	| edit_image_path(:id) |
|PATCH/PUT	| /photos/:id	| photos#update	| image_path(:id) |
|DELETE	| /photos/:id	| photos#destroy	| image_path(:id) |

#### Migrationについて
https://ruby-rails.hatenadiary.com/entry/20140810/1407634200#migration-change-datatype

#### scaffold削除
rails destroy scaffold task

#### DB検索方法
##### allメソッドとは
テーブルからすべてのレコードを取得する  
* Task.all
##### findメソッドとは
各モデルのidを検索キーとしてデータを取得するメソッド  
id以外の条件で検索不可  
取得したいデータのidの値が、1、10と具体的に分かっている場合に使用する。  
* Task.find(1)
##### find_byメソッドとは
各モデルをid以外の条件で検索するメソッド(idでも検索可能)  
複数の検索条件を指定可能  
返ってくる結果は、最初にヒットした1件のみ  
id及びid以外の条件が分かっている場合、その条件に該当する最初のデータを取得したい場合に使用する。  
* Task.find_by(status: 0)
##### whereメソッド
各モデルをid以外の条件で検索する場合  
該当するデータ全てが返ってくる。  
* Task.where(status: 0)

## Testについて
### Guardとは
guard-rspecはspecファイルに変更があった際、自動でテストを実行します。Gruntで言うgrunt-contribe-watchのようなものですね。  

Guard automates various tasks by running custom rules whenever file or directories are modified.  
It's frequently used by software developers, web designers, writers and other specialists to avoid mundane, repetitive actions and commands such as "relaunching" tools after changing source files or configurations.

####guardの導入
Gemfileに、
```
group :development, :test do
  gem 'guard-rspec', require: false
end
```
を追記して、bundle installします。  
次に、
```
bundle exec guard init
```
を実行し、Guardfileを生成します。

### RSpec とは

RSpec とは Ruby on Rails のテストフレームワークである。
つまりは、テスト専用のプログラム言語です。
RSpec は Gem パッケージとして提供されている。
RSpec の公式サイトは[こちら](https://github.com/rspec/rspec-rails)。  
##### Rails入門】RSpecを使ったテスト方法を初心者向けに基本から解説  
https://www.sejuku.net/blog/47847#RSpec

##### RSpec + Guard の導入の前に、RSpecのインストールが必要
Gemfileに
```
group :development, :test do
  gem 'rspec-rails'
end
```

##### RSpec導入後、rake db:migrateにMysql2::Error: Table 'myvueapp_development.schema_migrations' doesn't existが発生したら
```
group :development, :test do
  gem "factory_bot_rails"
end
```

#### Test Code Example 参考
https://ruby-rails.hatenadiary.com/entry/20141021/1413819783

#### コントローラー書くべきテストケースは基本的に5項目です。
1. 正常に動作しているか (http status)
2. 正常にHTTPメソッドを呼び出せているか (render template)
3. インスタンス変数が適切かどうか (assings)
4. モデルの増減 (change by)
5. リダイレクト (redirect)

4,5はコントローラ内に存在しない場合もある。
例えば、モデルの増減はDBにレコードがsave、update、destroyされた時に限る。


#### describe/context/itの使い分け
①describe : テスト対象が何か
②context : 条件は何か (with or whenから始める)
③it : アウトプットは何か
