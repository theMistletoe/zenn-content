---
title: "サインアウト機能の実装"
---

# サインアウト機能を足していく

サインアップ・サインイン機能ができたので、ひとまずサインアウト機能を足していきましょう。
指示するプロントは以下のとおりです。


```
This creates a basic user authentication function! Thank you very much!

Next, I would like to add a sign-out function.
The detailed functionality is as follows.

- Users can sign out.

Please give us the specific hands-on steps to achieve the functionality in a step-by-step manner that is easy for a beginner to understand.
```


:::details （和訳はこちら）
これで基本的なユーザー認証機能ができました！ありがとうございました！

次に、サインアウト機能を追加したいと思います。

詳しい機能は以下の通りです。

- ユーザーがサインアウトできる

この機能を実現するための具体的なハンズオンの手順を、初心者にもわかりやすくステップバイステップで教えてください。
:::

## 返答

レスポンスはこちらでした。

---

Devise gemを使用している場合、ログアウト機能はデフォルトで提供されています。ログアウトリンクを追加することで、ユーザーがログアウトできるようになります。

以下に、ログアウトリンクを追加する手順を示します。

ビューにログアウトリンクを追加します。以下のコードを適切な場所（例えばヘッダーやナビゲーションメニューなど）に追加します。


```
<% if user_signed_in? %>
  <%= link_to 'Sign out', destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to 'Sign in', new_user_session_path %> |
  <%= link_to 'Sign up', new_user_registration_path %>
<% end %>
```

このコードは、ユーザーがログインしている場合は「Sign out」リンクを、ログインしていない場合は「Sign in」と「Sign up」リンクを表示します。

以上で、ログアウト機能が追加されます。

# 追加の修正指示を仰ぐ

Gemで提供されているサインアウト機能を利用するようViewを修正する指示が回答されました。
が、このままではどのファイルにこの修正を適応すればよいかわかりません。

ので、聞いてみましょう。

```
Please let me know specifically which files I should modify and how.
```

:::details （和訳はこちら）
どのファイルをどのように修正すればいいのか、具体的に教えてください。
:::



## 返答


返答はこちらでした。


---

ログアウトリンクを追加するためには、通常、アプリケーションのレイアウトファイルを編集します。このファイルは通常、app/views/layouts/application.html.erbにあります。

以下のコードをこのファイルの<body>タグ内に追加します。適切な場所に追加してください（例えば、ヘッダーやナビゲーションメニューなど）。


```
<% if user_signed_in? %>
  <%= link_to 'Sign out', destroy_user_session_path, method: :delete %>
<% else %>
  <%= link_to 'Sign in', new_user_session_path %> |
  <%= link_to 'Sign up', new_user_registration_path %>
<% end %>
```

このコードは、ユーザーがログインしている場合は「Sign out」リンクを、ログインしていない場合は「Sign in」と「Sign up」リンクを表示します。

以上で、ログアウト機能が追加されます。


# エラーの修正


指示された修正を適応したところ、エラーが発生してしまいました。
このエラーも修正していきます。

```
The following error occurred Please tell me exactly how to correct it.

No route matches [GET] "/users/sign_out"
```

:::details （和訳はこちら）
以下のエラーが発生しました。修正方法を教えてください。
:::




## 返答

返答は以下の通り。

---


このエラーは、Deviseのデフォルト設定ではログアウトリンクがDELETEリクエストを期待しているのに対し、ブラウザがGETリクエストを送信しているために発生しています。

この問題を解決するためには、Deviseの設定を変更して、ログアウトリンクに対するGETリクエストを許可する必要があります。

以下の手順で設定を変更します。

1. config/initializers/devise.rb ファイルを開きます。

2. 以下の行を探します。

3. この行のコメントアウトを解除（#を削除）し、:deleteを:getに変更します。

```
config.sign_out_via = :get
```

4. 変更を保存し、Railsサーバーを再起動します。


以上の変更により、ログアウトリンクに対するGETリクエストが許可され、エラーが解消されるはずです。


---


この対応により、サインイン状態の際にはサインアウトのリンクが見えるようになりました。
サインアウト自体も機能しています。
(レイアウトファイルに記述することにより、サインイン画面では色々重複して見えていますが、一旦このままにしておきます。)

![signout](/images/dc953f02c4464a/image3_signout.png)
