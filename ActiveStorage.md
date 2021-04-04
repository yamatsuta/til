##### Active Storageの仕組み、構造

ユーザーのアバターだからといって、ユーザーテーブルにパスを保存しない

`active_storage_attachments`テーブルが外部キーを二つ持つことで`users`テーブルと`active_storage_blobs`テーブルとの橋渡しを担っている

実際のファイルのパス名の保存は`active_storage_blobs`が担っている

実際のファイル実体の保存は`amazon S3`ストレージが担っている

##### 今後は、三つのテーブルに気を配る必要があるということ

##### とはいえ、直接`active_storage_attachments`と`active_stroge_blobs`テーブルに干渉する必要はないみたい

***
#### Active Storageの利点

貼り付けたいモデル（Userとか）に画像（やファイル）用のカラムを用意する必要がない

例えば画像なら、画像用のモデル（Imageモデルとか）を用意する必要がない　裏で`article.image`とか使えるようにしてくれる

上に関連して、カラムを（目に見える形で）用意していないのに、さもそのカラムがあるかのように記述できる

以下はテンプレートな書き方

```
class CommentsController < ApplicationController
  def new
    @comment = Comment.new
  end

  def create
    @comment = Comment.create params.require(:comment).permit(:content, :image) # POINT
    redirect_to @comment
  end

  def show
    @comment = Comment.find(params[:id])
  end

  def edit
    @comment = Comment.find(params[:id]) 
  end

  def update
    @comment = Comment.find(params[:id])
    @comment.update params.require(:comment).permit(:content, :image) # POINT
    redirect_to @comment
  end
end
```

```
<%= form_with model: @comment, local: true  do |form| %>
  <%= form.text_area :content %><br>
  <%= form.file_field :image %><br>
  <%= form.submit %>
<% end %>
```

```
<% if @comment.image.attached? %>
  <%= image_tag @comment.image %>
<% end %>
```

`file_field`の属性にimageを指定して、

paramsでimageを受け取って、保存している　`comments`テーブルにはimageカラムなんてないのに

実際にそのカラムを扱っているのは`active_storage_attachments`と`active_stroge_blobs`テーブル **便利！**

***

#### 貼り付けるのは単一ファイルか、複数のファイルか

貼り付けたい（関連づけたい）ファイルが単一の場合は`has_one_attached`を使う

複数の場合は`has_maney_attached`を使う

`file_field`に`multiple: true`を追記して、複数ファイルの選択を許可

送り方：`<%= form.file_field :images, multiple: true %><br>`

受け取り方：`params.require(:comment).permit(:content, images: [])`

表示：

```
<% if @comment.images.attached? %>
  <% @comment.images.each do |image| %>
    <%= image_tag image %> <br>
  <% end %>
<% end %>
```

