---
layout: post
title: "Rails5対応 自分だけログインできる環境を作る"
date: 2017-11-13 9:41:28 +0900
categories: rails
---

[devise](https://github.com/plataformatec/devise) gemを使うことで簡単にログイン環境と認可機能を実装できますが、**自分しか使わない個人のアプリだけどHerokuにアップするから他の人がeditとかnewのページに入れないようにしたい**ぐらいだと、gem使うよりも独自に実装するほうが簡単だったのでご紹介。

## 参考サイト
ほとんど、このブログ記事を参考にしました。[Ruby on Rails3.2でログイン機能を実装する。](http://starryskylogic.blogspot.jp/2012/04/ruby-on-rails32.html)

## 準備
Gemfileに書かれているがコメントアウトされている `bcrypt` gem を有効にします。パスワードの暗号化に必要です。

```rb
# Gemfile
gem 'bcrypt', '~> 3.1.7'
```

そして、 `bundle install` をして準備完了。

## Userモデル作成
まず、Userモデルを作ります。

```shell-session
$ rails g model user name:string password_digest:string
$ rails db:migrate
```

次にできたUserモデルファイルを変更

```ruby
# app/model/user.rb
class User < ApplicationRecord
  has_secure_password
  validates :password, presence: true, length: { minimum: 8 }
end
```

これでさっき入れたbcrypt gemが使われます。

## データ作成
そして、アカウント追加

```shell-session
$ rails c
irb(main):001:0> User.create!(name: "admin", password: "hogehoge", password_confirmation: "hogehoge")
```

passwordはハッシュ化された値で保存されます。

## Sessionコントローラ作成
ログイン環境のsessionコントローラを生成

```shell-session
$ rails g controller sessions
```

## ルートに追加

```ruby
# config/routes.rb
resources :sessions, only: %i(new create destroy)
```

## コントローラ実装

```ruby
# app/controllers/sessions_controller.rb
class SessionController < ApplicationController
  def new; end

  def create
    user = User.find_by(name: params[:name])
    if user && user.authenticate(params[:pass])
      session[:user_id] = user.id
      redirect_to root_path
    else
      flash.now.alert = "該当するデータがありませんでした"
      render :new
    end
  end

  def destroy
    session[:user_id] = nil
    redirect_to root_path
  end
end
```

## Viewでログインフォームを作る

```erb
# app/views/sessions/new.erb
<h1>ログイン</h1>
<%= form_tag sessions_path do %>
  <div class="field">
    <%= label_tag :name, 'login name' %>
    <%= text_field_tag :name, params[:name] %>
  </div>
  <div class="field">
    <%= label_tag :pass, 'password' %>
    <%= password_field_tag :pass , params[:pass]%>
  </div>
  <div class="actions">
    <%= submit_tag "ログイン" %>
  </div>
<% end %>
```

これで、createするときにセッション情報をブラウザのCookieに追加する。

## ヘルパーメソッド追加
ヘルパーメソッドでログイン情報を取得できるようにします。

```ruby
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  helper_method :current_user

  def current_user
    @current_user ||= User.find(session[:user_id]) if session[:user_id]
  end
end
```

## ログインリンクを追加
ログインへのリンクを置きたいViewに以下のコードを追加します。

```erb
<% if current_user.nil? %>
  <%= link_to "ログイン", new_session_path %>
<% else %>
  <%= link_to "ログアウト", session_path(current_user.id), method: :delete, data: { confirm: 'ログアウトしますか？' } %>
<% end %>
```

## Controllerでログインしていないユーザをはじく

ログインしているユーザしかアクセスできないようにする場合には該当の controller の before_action で指定してあげます。
次のコード例は posts_controller の new edit create update destroy アクションへのアクセスをログインユーザのみに制限する場合です。ログインしていない場合は root_path へ redirect させます。

```ruby
# app/controllers/posts_controller.rb
class PostsController < ApplicationController
  before_action :login_check, only: %i(new edit create update destroy)
  before_action :set_post, only: %i(show edit update destroy)

  def index
    @posts = Post.all
  end

  def show; end

  def new
    @post = Post.new
  end

  def edit; end

  def create
    @post = Post.new(post_params)

    respond_to do |format|
      if @post.save
        format.html { redirect_to @post, notice: 'Post was successfully created.' }
        format.json { render :show, status: :created, location: @post }
      else
        format.html { render :new }
        format.json { render json: @post.errors, status: :unprocessable_entity }
      end
    end
  end

  def update
    respond_to do |format|
      if @post.update(post_params)
        format.html { redirect_to @post, notice: 'Post was successfully updated.' }
        format.json { render :show, status: :ok, location: @post }
      else
        format.html { render :edit }
        format.json { render json: @post.errors, status: :unprocessable_entity }
      end
    end
  end

  def destroy
    @post.destroy
    respond_to do |format|
      format.html { redirect_to posts_url, notice: 'Post was successfully destroyed.' }
      format.json { head :no_content }
    end
  end

  private

  def login_check
    if session[:user_id].nil?
      redirect_to root_path
    end
  end

  def set_post
    @post = Post.find(params[:id])
  end

  def post_params
    params.require(:post).permit(:title, :content)
  end
end
```