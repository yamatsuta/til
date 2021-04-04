#### carrier_waveでできること

任意の名前のアップローダークラス（ファイル）の作成

`mount_uploader`メソッドの提供

ファイルの永続的な保存（ストア）と、一時的な保存（キャッシュ）

ファイルシステムや、クラウドストレージなど、ファイルの保存方式の選択肢

複数ファイルのアップロード

ファイルの保存場所の変更

拡張子のホワイトリスト化　不正を検知したらアップロードを無効にするかエラーを起こす

逆にブラックリストも作成できる

##### アップローダーの具体的な使い方

```
class MyUploader < CarrierWave::Uploader::Base
  include CarrierWave::MiniMagick

  process resize_to_fit: [800, 800]

  version :thumb do
    process resize_to_fill: [200,200]
  end

end
```

```
uploader = AvatarUploader.new
uploader.store!(my_file)                              # size: 1024x768

uploader.url # => '/url/to/my_file.png'               # size: 800x800
uploader.thumb.url # => '/url/to/thumb_my_file.png'   # size: 200x200
```

`version`はネストさせることができる

画像の属性や画像自体によって、条件分けすることができる

バリデーション失敗時にも、送信されたはずの画像をちゃんと残して再度render表示することができる

例）avatar画像を送ろうとしてバリデーション失敗したとき

```
<%= form_for @user, html: { multipart: true } do |f| %>
  <p>
    <label>My Avatar</label>
    <%= f.file_field :avatar %>
    <%= f.hidden_field :avatar_cache %>
  </p>
<% end %>
```

意訳：ファイルがアップロードされたことをユーザーに示すのは良いアイデアかもしれませんが、画像の場合は、小さなサムネイルが良い指標になります。

```
<%= form_for @user, html: { multipart: true } do |f| %>
  <p>
    <label>My Avatar</label>
    <%= image_tag(@user.avatar_url) if @user.avatar? %>
    <%= f.file_field :avatar %>
    <%= f.hidden_field :avatar_cache %>
  </p>
<% end %>
```

アップロードしたファイルの削除は、チェックボックスを追加するだけで実装できる

```
    <省略>
    <label>
      <%= f.check_box :remove_avatar %>
      Remove avatar
    </label>
```

手動で消すなら`remove_avatar!`メソッド呼び出しからの、保存

##### できること、その他

URL によってインターネット上の場所からファイルをアップロードすること

デフォルト画像(ファイル)のurlを指定できる`default_url`メソッドの提供

##### あと、諸々configで設定できる

##### テストに関しても、configをいじると便利　RSpecのために便利なマッチャも用意しているので使うとき確認するといいよ

アップローダーをモデルにマウントすることで、いくつかのコールバックが使えるようになる

* before_save

* after_save

* after_commit など

