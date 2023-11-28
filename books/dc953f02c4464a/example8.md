---
title: "完了ステータスの切り替えをやりやすくする"
---

# チェックボックスだけでステータスを切り替えられる

今の実装ですと、完了/未完了のステータス変更にいちいち更新ボタンを押さないといけないです。
これは面倒ですね。

チェックボックスだけでステータスを切り替えられるようにしましょう

指示はこちらです。

```
@workspace /explain In the current implementation, when changing the completion status of a TODO item, you must switch the checkbox and then click the update button. Clicking the update button every time is a pain, so when I want to switch the completion status of a TODO item, I would like to be able to switch the status, including updating the status of the DB data, just by clicking the checkbox.

Please provide specific hands-on steps on how to modify the implementation.
```


:::（和訳はこちら）
現在の実装では、TODO項目の完了ステータスを変更する場合、チェックボックスを切り替えてから更新ボタンをクリックする必要がある。毎回更新ボタンをクリックするのは面倒なので、TODO項目の完了ステータスを切り替えたいときに、チェックボックスをクリックするだけで、DBデータの更新も含めてステータスを切り替えられるようにしてほしい。

実装の修正方法について、具体的なハンズオンの手順を教えてください。
:::


## 返答

返答はこちらです。

---

この要件を満たすためには、JavaScriptを使用してチェックボックスがクリックされたときにフォームを自動的に送信するようにします。また、Railsのremote: trueオプションを使用して、非同期通信でデータベースの更新を行います。

以下の手順で実装します。

1. フォームにremote: trueオプションを追加します。これにより、フォームはAjaxを使用して送信されます。

```
<%= form_with model: todo, local: true, remote: true do |form| %>
```

2. チェックボックスにJavaScriptのイベントリスナーを追加します。これにより、チェックボックスがクリックされたときにフォームが自動的に送信されます。


```
<%= form.check_box :completed, onchange: 'this.form.submit()' %>
```

3. todos_controller.rbでupdateアクションを修正します。非同期通信に対応するために、format.jsを追加します。


```ruby
def update
  @todo = Todo.find(params[:id])
  @todo.update(todo_params)

  respond_to do |format|
    format.html { redirect_to todos_path }
    format.js
  end
end
```


views/todosディレクトリにupdate.js.erbファイルを作成します。このファイルでは、更新後のTODOの状態に応じてチェックボックスの見た目を更新します。

```js
// 例えば、以下のように実装できます。
var checkbox = document.getElementById('<%= dom_id(@todo) %>');
checkbox.checked = <%= @todo.completed %>;
```

以上の変更により、TODOの完了状態をチェックボックスで切り替えると、自動的にデータベースが更新されるようになります。


# なんだかよく分からない…


Ruby on Rails初診者なだけあって、何がなんだかよくわからないですね…笑



[WIP]





Please show the entire code after correction.



ご指定いただいたコードを修正すると、以下のようになります。

```html
<!-- app/views/todos/index.html.erb -->
<%= form_with model: Todo.new, local: true do |form| %>
  <%= form.text_field :title %>
  <%= form.submit '+' %>
<% end %>

<ul>
  <% @todos.each do |todo| %>
    <li>
      <%= form_with model: todo, local: true, remote: true do |form| %>
        <%= form.check_box :completed, onchange: 'this.form.submit()' %>
        <%= form.label :title, todo.title %>
        <!-- Updateボタンは不要になるので削除します -->
      <% end %>
    </li>
  <% end %>
</ul>
```


この修正により、TODOの完了状態を切り替えるたびにフォームが自動的に送信され、データベースが更新されます。ただし、これを動作させるためには、サーバーサイドで非同期通信を処理する必要があります。そのため、todos_controller.rbのupdateアクションと、対応するJavaScriptのビューも修正する必要があります。










---








update todos_controller.rb update function.




todos_controller.rbのupdateメソッドを以下のように修正します。


```ruby
def update
  @todo = current_user.todos.find(params[:id])
  if @todo.update(todo_params)
    respond_to do |format|
      format.html { redirect_to root_path }
      format.js
    end
  else
    render :index
  end
end
```

この変更により、updateメソッドは非同期通信（Ajax）に対応します。HTML形式のリクエストの場合は従来通りリダイレクトしますが、JavaScript形式のリクエストの場合はupdate.js.erbをレンダリングします。このupdate.js.erbはまだ作成していないので、次に作成します。



----




make update.js.erb.


update.js.erbファイルを作成すると、以下のようになります。

```js
// app/views/todos/update.js.erb
var todoElement = document.getElementById('todo_<%= @todo.id %>');
var checkbox = todoElement.querySelector('input[type="checkbox"]');
checkbox.checked = <%= @todo.completed ? 'true' : 'false' %>;
```

このコードは、更新されたTODOのチェックボックスの状態を、データベースの状態に合わせて更新します。@todo.completedがtrueならチェックボックスをチェックし、falseならチェックを外します。

ただし、このコードが正しく動作するためには、各TODOのHTML要素に一意のIDを設定する必要があります。例えば、以下のように設定できます。

```html
<!-- app/views/todos/index.html.erb -->
<% @todos.each do |todo| %>
  <li id="todo_<%= todo.id %>">
    <%= form_with model: todo, local: true, remote: true do |form| %>
      <%= form.check_box :completed, onchange: 'this.form.submit()' %>
      <%= form.label :title, todo.title %>
    <% end %>
  </li>
<% end %>
```
