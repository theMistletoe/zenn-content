---
title: "認証機能の追加"
---

# 認証機能を足す指示

続いて、基本的な認証機能を作っていきましょう。
指示するプロントは以下のとおりです。


> I was able to create a basic TODO management function! Thank you very much.
I would like to add user authentication functionality here.
Here is what I would like to achieve
> - Users can sign up with their email address and password.
> - Users can log in with their email address and password.
> - Users can only view and manipulate their own TODOs after logging in.
> - Users can only view and manipulate their own TODOs after logging in. Users cannot view or manipulate other users' TODOs.

> Please suggest step-by-step, easy-to-understand hands-on procedures for beginners to achieve these functions. Please present the commands that should be executed in the terminal and the code description in a form that can be copied and pasted in its entirety.



:::details （和訳はこちら）

基本的なTODO管理機能を作ることができました！ありがとうございました。

ここにユーザー認証機能を追加したいと思います。

実現したいことは以下の通りです。

- ユーザーはメールアドレスとパスワードでサインアップできます。
- ユーザーはメールアドレスとパスワードでログインできます。
- ユーザーはログイン後、自分のTODOのみを閲覧・操作できる。
- ユーザーはログイン後、自分のTODOのみを閲覧・操作することができます。他のユーザーのTODOを閲覧・操作することはできません。

これらの機能を実現するための、初心者にもわかりやすい実践的な手順を提示してください。ターミナルで実行すべきコマンドとコードの説明を、そのままコピー＆ペーストできる形で提示してください。

:::



# 返答


こういうレスポンスが返ってきました。

---


> ユーザー認証機能を追加するためには、Deviseというgemを使用するのが一般的です。以下に、Deviseを使用してユーザー認証機能を追加する手順を示します。

> Devise gemをインストールします。Gemfileに以下の行を追加し、bundle installコマンドを実行します。

```
# Gemfile
gem 'devise'
```

> ターミナルで以下のコマンドを実行します。

```
bundle install
```

> Deviseをセットアップします。ターミナルで以下のコマンドを実行します。

```
rails generate devise:install
```

> Userモデルを作成します。ターミナルで以下のコマンドを実行します。

```
rails generate devise User
```


> マイグレーションを実行します。ターミナルで以下のコマンドを実行します。

```
rails db:migrate
```


> ユーザーがログインしていることを確認するために、before_actionをTodosControllerに追加します。

```ruby
# app/controllers/todos_controller.rb
before_action :authenticate_user!
```

> Todoモデルにユーザーとの関連付けを追加します。

```ruby
# app/models/todo.rb
belongs_to :user
```

> Userモデルにtodosとの関連付けを追加します。

```ruby
# app/models/user.rb
has_many :todos
```

> Todosテーブルにuser_idカラムを追加します。ターミナルで以下のコマンドを実行します。

```
rails generate migration AddUserToTodos user:references
rails db:migrate
```

> TodosControllerの各アクションを更新して、現在のユーザーのTODOのみを操作するようにします。

```ruby
# app/controllers/todos_controller.rb
def index
  @todos = current_user.todos
end

def create
  @todo = current_user.todos.build(todo_params)
  # 以下省略
end

def update
  @todo = current_user.todos.find(params[:id])
  # 以下省略
end

def destroy
  @todo = current_user.todos.find(params[:id])
  # 以下省略
end
```


> 以上で、ユーザー認証機能とユーザーごとのTODO管理機能が追加されます。


---


ユーザー認証によく使われるGemを利用することでユーザー認証を実装するようにしているようです。

コードの修正箇所が断片的なので、開発者でないと理解できないかもしれませんね。
コード全体を提示してもらうような指示を出せば、コードをコピペしやすいようになるかもしれません。

とにかく手順に沿ってコピペを進めていきます。


# エラーへの対処


提示の手順どおりにすすめていると、migrationのコメントでエラーが出てしまいました。
以下のようにお願いして、修正してもらいましょう。



> The following error occurred when running rails db:migrate. Please tell me exactly how to fix it.
===
$ rails db:migrate
== 20231125150937 AddUserToTodos: migrating ===================================
-- add_reference(:todos, :user, {:null=>false, :foreign_key=>true})
rails aborted!
StandardError: An error has occurred, this and all later migrations canceled:
SQLite3::ConstraintException: NOT NULL constraint failed: todos.user_id
/workspaces/codespaces-rails/db/migrate/20231125150937_add_user_to_todos.rb:3:in `change'
Caused by:
ActiveRecord::NotNullViolation: SQLite3::ConstraintException: NOT NULL constraint failed: todos.user_id
/workspaces/codespaces-rails/db/migrate/20231125150937_add_user_to_todos.rb:3:in `change'
Caused by:
SQLite3::ConstraintException: NOT NULL constraint failed: todos.user_id
/workspaces/codespaces-rails/db/migrate/20231125150937_add_user_to_todos.rb:3:in `change'
Tasks: TOP => db:migrate
(See full trace by running task with --trace)


## 返答

返答は以下となりました。

---

> このエラーは、既存のTodoレコードがuser_idを持っていないために発生しています。新たにuser_idカラムを追加し、それがnullでないことを要求しているため、既存のTodoレコードがこの制約に違反しています。

> この問題を解決するためには、マイグレーションファイルを修正して、user_idカラムをnull許可にするか、既存のTodoレコードに適当なuser_idを設定する必要があります。

> 以下に、user_idカラムをnull許可にするマイグレーションファイルの修正例を示します。

```ruby
class AddUserToTodos < ActiveRecord::Migration[6.0]
  def change
    add_reference :todos, :user, null: true, foreign_key: true
  end
end
```

> この修正を行った後、再度rails db:migrateコマンドを実行してください。

----

なるほど、DBの整合性エラーに引っかかっているということですね。
デバッグ中にデータを作ったりしていたので、原因としては正しそうです。

提案の通り制約を緩めて対応しても良いですが、ローカルで取っておきたいデータも似ですし、データを削除する方針で対応しましょう。


> I would like to completely delete all existing records once and for all. Please let me know how to implement this.
（既存のレコードを完全に削除したいのですが、どうすればよいでしょうか？実装方法を教えてください。）


返答は以下となりました。

---

> 既存のレコードをすべて削除するには、以下のコマンドをターミナルで実行します。

```sh
rails db:drop db:create db:migrate
```

> このコマンドは、以下の3つの操作を順に実行します。
> 1. db:drop - データベースを削除します。
> 2. db:create - 新しいデータベースを作成します。
> 3. db:migrate - マイグレーションを実行して、新しいデータベーススキーマを作成します。

> これにより、すべての既存レコードが削除され、新しいデータベースが作成されます。ただし、この操作は元に戻すことができないため、重要なデータが失われないように注意してください。

---







この対応により、migrationの手順を実行できるようになりました！

後続の手順をすべて進め、アプリの状態は以下のようになりました。


![TODOappImage2](/images/dc953f02c4464a/image2.png)

サインイン・サインアップの導線ができるようになりました。
これ自体はほぼGemのママ利用しているだけだと思いますが、機能としてもきちんと想定通り動いており、サインイン・サインアップができ、自身のTODOのみを確認できるようになりました。



