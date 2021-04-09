#### pundit

##### ヘルパーの形式で、簡潔かつスケーラビリティ（拡張性があるってこと）な、認証システムを提供する

```
gem 'pundit'
```

```
# アプリケーションポリシーをセットアップする
rails g pundit:install
```

> gコマンドした後はサーバーを再起動した方がいい

```
# Pundit(モジュール)をアプリでインクルードする
class ApplicationController < ActionController::Base
  include Pundit
```

コントローラーに対応したポリシーファイルを作成する

アクションに対応したポリシーを定義する

アクション内で、authorize メソッドでポリシーを呼び出している

（authorizeメソッドの引数に指定した「リソース」に対して、そのアクションを許可されているユーザーか否かを判定する）

**authorizeメソッドは、引数に指定したモノから自動的にポリシーメソッドを選んでくれる**

```
unless PostPolicy.new(current_user, @post).update?
  raise Pundit::NotAuthorizedError, "not allowed to update? this #{@post.inspect}"
end
```

現在のユーザーがリソースでアクション成功できない限り、

raiseだからエラーになる　Pundit::NotAuthorizedError

```
# viewで権限のあるなし判定により、表示・非表示ができる
<% if policy(@post).update? %>
  <%= link_to "Edit post", edit_post_path(@post) %>
<% end %>
```

Policyモジュールがgemによって提供されていて

そのインスタンスの作成に成功するかしないかが、権限のあるなしの判定として機能しているよう

```
# 第二引数に任意のポリシーメソッドを指定することができる
def publish
  @post = Post.find(params[:id])
  authorize @post, :index?
  # ...
end
```

↑ これ、ポリシーファイルにpublish用意して、その中でindexメソッドを参照させれば同じことができそう

```
# 別モデルで作ったインスタンスを認可したい場合
def update
  @hoge = Hoge.find(params[:id])
  authorize @hoge, policy_class: PostPolicy
  # ...
end
```

第二引数にハッシュ形式で指定したいポリシーモデルを書けばいい



##### リソースに対するユーザー権限の可否を書いているのがポリシーファイルなので、ただtrueなら全員ok falseなら全員ダメ　特定のユーザーにだけ許可するなら`user.admin? || user.editor?` みたいな書き方になる



***

### 
