---
title: "アプリの骨子を作る"
---

# 最初の指示

それでははじめの指示を行っていきましょう。
最初に出す指示はこんな感じにします。
ユーザー視点でできることを意識して、プロンプトを書いてみました。


---

```
I am looking to create a simple TODO app. The application framework to be used will be Ruby on Rails.

Here is what you can do with this application

- The user can see a form to enter the title of the TODO.
- The user sees a + button.
- When the user enters text in the form to enter a TODO title and presses the + button, the TODO item with the entered title is added to the TODO list and can be viewed.
  - TODO items have visible titles and checkboxes.
- The user can click on the checkbox of a TODO item without a checkbox to save it as checked.
- The user can click the checkbox of a TODO item with a checkbox to save it as an unchecked state.

Please give us hands-on, step-by-step instructions on how to create an app with this kind of functionality in a way that is easy for beginners to understand!
```

:::details （和訳はこちら）



シンプルなTODOアプリを作りたいと思っています。使用するアプリケーションフレームワークはRuby on Railsです。

このアプリケーションでできることは以下の通りです。

- ユーザーはTODOのタイトルを入力するフォームを見ることができます。
- ユーザーには+ボタンが見えます。
- ユーザーがTODOのタイトルを入力するフォームにテキストを入力し、+ボタンを押すと、入力されたタイトルのTODO項目がTODOリストに追加され、見ることができる。
  - TODO項目には、目に見えるタイトルとチェックボックスがあります。
- チェックボックスのないTODO項目のチェックボックスをクリックすると、チェックされた状態で保存されます。
- ユーザーは、チェックボックスのあるTODO項目のチェックボックスをクリックして、チェックされていない状態として保存することができます。

このような機能を持つアプリの作り方を、初心者にもわかりやすく、実践的にステップ・バイ・ステップで教えてください！
:::




# 返答

これに対するレスポンスは以下となりました。

---

Ruby on Railsを使用してこのようなTODOアプリケーションを作成するための手順は以下の通りです。

1. 新しいRailsアプリケーションを作成します。コマンドラインでrails new todo_appを実行します。
2. Todoモデルを作成します。 `rails generate model Todo title:string completed:boolean` を実行します。
3. データベースマイグレーションを実行します。 `rails db:migrate` を実行します。
4. TodosControllerを作成します。`rails generate controller Todos`を実行します。
5. config/routes.rbファイルを編集して、ルートURLがTodosControllerのindexアクションにルーティングされるようにします。
6. app/views/todos/index.html.erbビューファイルを作成し、TODOのタイトルを入力するフォームとTODOリストを表示する領域を作成します。
7. TodosControllerのindexアクションで、すべてのTODOを取得し、それらをビューに渡します。
8. TodosControllerに新しいTODOを作成するためのcreateアクションを追加します。このアクションは、フォームから送信されたパラメータを使用して新しいTODOを作成し、データベースに保存します。
9. TodosControllerにTODOの完了状態を更新するためのupdateアクションを追加します。このアクションは、チェックボックスがクリックされたときに呼び出され、対応するTODOのcompleted属性を更新します。

以下に、これらの手順を詳細に説明します。

```ruby
# config/routes.rb
Rails.application.routes.draw do
  resources :todos, only: [:index, :create, :update]
  root to: 'todos#index'
end
```

```ruby
# app/controllers/todos_controller.rb
class TodosController < ApplicationController
  def index
    @todos = Todo.all
  end

  def create
    @todo = Todo.new(todo_params)
    if @todo.save
      redirect_to root_path
    else
      render :index
    end
  end

  def update
    @todo = Todo.find(params[:id])
    if @todo.update(todo_params)
      redirect_to root_path
    else
      render :index
    end
  end

  private

  def todo_params
    params.require(:todo).permit(:title, :completed)
  end
end
```

```html
<!-- app/views/todos/index.html.erb -->
<%= form_with model: Todo.new, local: true do |form| %>
  <%= form.text_field :title %>
  <%= form.submit '+' %>
<% end %>

<ul>
  <% @todos.each do |todo| %>
    <li>
      <%= form_with model: todo, local: true do |form| %>
        <%= form.check_box :completed %>
        <%= form.label :title, todo.title %>
        <%= form.submit 'Update' %>
      <% end %>
    </li>
  <% end %>
</ul>
```

このコードは、最も基本的なTODOアプリケーションを作成します。ユーザーがTODOタイトルを入力し、+ボタンをクリックすると、新しいTODO項目がリストに追加されます。
各TODO項目にはチェックボックスがあり、チェックボックスをクリックすると項目の完了状態が更新されます。


# 結果


これらの指示どおりコマンド実行やコード修正をしていきます。

これらの修正を適応した状態がこちら↓


![TODOapp](/images/dc953f02c4464a/image.png)


基本的な新規のTODOの作成とステータスの更新ができるようになりました。
この程度のことであれば、1回のやり取りで作ることができました！
ステータスの更新がいちいちUpdateボタンを押さないと行けないのが面倒ですね。

---

ともあれ、これで基本的なベースができました！
ここから機能を追加していきます。






