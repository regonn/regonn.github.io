---
layout: post
title: "Rails初心者が4.0.0のScaffold生成コードを調べてみた"
date: 2014-07-06 9:41:28 +0900
categories: rails
---

# はじめに
今までRailsのscaffoldは、素早くアプリを作るためのツールだと思っていました。しかし、scaffoldはRailsの規約が適用されているバイブル的な存在であり、scaffoldに従って書くことで効率的にサイトを作っていくことができる！と教わったので、生成されるコードで気になる所を調べてみました。その時のメモ。

※このメモを書いたのは2013年7月で書いた時点ではRails触って3ヶ月位だったので情報が間違っているor古くなっている可能性があります。

# scaffold差分
[生成されるコード](https://github.com/regonn/scaffold/commit/538f1173ff974537337cfb371a50658696b65460)
実行コード

``` sh
$ rails generate scaffold Item name:string price:integer description:text
```

## migrateファイル

``` ruby
class CreateItems < ActiveRecord::Migration
  def change
    create_table :items do |t|
      t.string :name
      t.integer :price
      t.text :description

      t.timestamps
    end
  end
end
```

- ActiveRecordのclassを継承していますが、これはSQLでリレーショナルデータベースを扱う際に必要なもの

[Ruby on Rails3で学ぶWeb開発のキホン（3）：「ActiveRecord」の基本とデータの参照 (1/2) - ＠IT](http://www.atmarkit.co.jp/ait/articles/1104/12/news135.html)

- NOSQLでは使われないみたい


[MongoDB を Rails から使う、導入編 - happy lie, happy life](http://d.hatena.ne.jp/spitfire_tree/20120913/1347508860)

これで無事に、migrateができました。失敗したorやり直したい時には

``` sh
$ rake db:migrate:redo [STEP=ステップ数]
```

で戻すことができます。

## config/routes.rb

``` ruby
Sample::Application.routes.draw do
  resources :items
end
```

- resources :itemsがscaffoldで生成されています。これによって、routingが追加されてきます。ちなみに、 resource :itemとすれば、単体で扱う場合に重宝するみたいです。

[Ruby on Rails ルーティング - ayaketanのプログラミング勉強日記](http://ayaketan.hatenablog.com/entry/2012/12/19/224456)

- 設定されたroutingは `rake routes` コマンドで確認ができます。

```
   Prefix Verb   URI Pattern               Controller#Action
    items GET    /items(.:format)          items#index
          POST   /items(.:format)          items#create
 new_item GET    /items/new(.:format)      items#new
edit_item GET    /items/:id/edit(.:format) items#edit
     item GET    /items/:id(.:format)      items#show
          PATCH  /items/:id(.:format)      items#update
          PUT    /items/:id(.:format)      items#update
          DELETE /items/:id(.:format)      items#destroy
```

- Rails3とは違ってPATCHが追加されています。

[次期RailsがPATCHメソッドを採用 - ぶろぐ。@はてな](http://d.hatena.ne.jp/tkawa/20120325/p1)

## app/controller/items_controller.rb

``` ruby
class ItemsController < ApplicationController
  before_action :set_item, only: [:show, :edit, :update, :destroy]

  def index
    @items = Item.all
  end

  def show; end

  def new
    @item = Item.new
  end

  def edit; end

  def create
    @item = Item.new(item_params)

    respond_to do |format|
      if @item.save
        format.html { redirect_to @item, notice: 'Item was successfully created.' }
        format.json { render action: 'show', status: :created, location: @item }
      else
        format.html { render action: 'new' }
        format.json { render json: @item.errors, status: :unprocessable_entity }
      end
    end
  end

  def update
    respond_to do |format|
      if @item.update(item_params)
        format.html { redirect_to @item, notice: 'Item was successfully updated.' }
        format.json { head :no_content }
      else
        format.html { render action: 'edit' }
        format.json { render json: @item.errors, status: :unprocessable_entity }
      end
    end
  end

  def destroy
    @item.destroy
    respond_to do |format|
      format.html { redirect_to items_url }
      format.json { head :no_content }
    end
  end

  private

    def set_item
      @item = Item.find(params[:id])
    end

    def item_params
      params.require(:item).permit(:name, :price, :description)
    end
end
```

- before_actionを使うことで、 `@item = Item.find(params[:id])` を実行しているので、showやeditメソッドがスッキリしてますね。

- action以外のメソッドを追加してしまうと、意図しないコードが直接実行されてしまう可能性があるので、privateメソッドにする必要があるみたいです。

- Rails3でのattr_accessibleはstrong parametersに変更。

[Rails 4でmass assignmentを防止する方法: strong parameters - memo.yomukaku.net](http://memo.yomukaku.net/entries/sNBzYUI)

- renderとredirect_toの違いですが、renderはそのままデータをviewテンプレートに貼り付けるイメージで、redirectはもう一度URLにリクエストして表示しています。もし、createメソッドのredirect_toをrenderに変更してしまうと、動作はしますが、クライアント側でブラウザバック等を使って何回も容易にに新しいデータをcreateできてしまう危険性が生まれてしまいます。

[ruby on rails - Are redirect_to and render exchangeable? - Stack Overflow](http://stackoverflow.com/questions/7493767/are-redirect-to-and-render-exchangeable)
(英語ですが一番図が分かりやすかったです。redirectすると間にGETリクエストが入るのが大事みたい。)

##app/views/items/index.html.erb

``` erb
<h1>Listing items</h1>

<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Price</th>
      <th>Description</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>

  <tbody>
    <% @items.each do |item| %>
      <tr>
        <td><%= item.name %></td>
        <td><%= item.price %></td>
        <td><%= item.description %></td>
        <td><%= link_to 'Show', item %></td>
        <td><%= link_to 'Edit', edit_item_path(item) %></td>
        <td><%= link_to 'Destroy', item, method: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
</table>

<br>

<%= link_to 'New Item', new_item_path %>
```

- ほとんどは、htmlが理解できていれば大丈夫そうですが、注目するのはedit_item_path(item)の部分ですね。

controllerのところではredirect_to items_urlが使われていて、XXX_path と XXX_urlで何が違うのか知らなかった。調べてみると相対パスと絶対パスの違いがあるみたい。（link_toなら出力する量が少ないXXX_pathの方がいいらしい）
[Railsの*_pathと*_urlに関するメモ - $ cat /var/log/shin](http://shin.hateblo.jp/entry/2013/01/13/124158)

## app/views/items/index.json.jbuilder
rails4からJSON出力にjbuilderが使われているみたいです。

``` jbuilder
json.array!(@items) do |item|
  json.extract! item, :name, :price, :description
  json.url item_url(item, format: :json)
end
```

- これで、JSON形式の出力もview側に書けばいいのでcontrollerがスッキリするみたいですね。

[rails4のjbuilderを使ってみた - ガイアが俺にもっとコード書けと囁いている](http://f96q.github.io/blog/2013/03/05/rails4-jbuilder/)

## app/views/items/_form.html.erb

``` erb
<%= form_for(@item) do |f| %>
  <% if @item.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(@item.errors.count, "error") %> prohibited this item from being saved:</h2>

      <ul>
      <% @item.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
      </ul>
    </div>
  <% end %>

  <div class="field">
    <%= f.label :name %><br>
    <%= f.text_field :name %>
  </div>
  <div class="field">
    <%= f.label :price %><br>
    <%= f.number_field :price %>
  </div>
  <div class="field">
    <%= f.label :description %><br>
    <%= f.text_area :description %>
  </div>
  <div class="actions">
    <%= f.submit %>
  </div>
<% end %>
```

- 実際にどのように変換されるのかは次のサイトが分かりやすかった

[「Ruby on Rails Tutorial」ビューのform_forと実際に生成されたHTMLフォーム Ruby on RailsでWebサイト公開！に挑戦中](http://hbnist76.blog.fc2.com/blog-entry-144.html)

# おわりに

今回はこれで終了。

Railsの「設定よりも規約」という考えが反映され、普段気にせず使っているコードも、理解を深めていけばいくほどプログラマーにとって便利な動きをしてくれていることがわかってきました。

今回scaffoldを追ってみたことで、以前よりはレールに乗れて脱線することも減るかなと思います。