#### wheneverの復習

##### cronジョブを記述あるいはデプロイするためのコマンドを提供するgem

生cronよりは、かなりわかりやすくcronジョブを作成することができる

install：

```
gem 'whenever', require: false
```

##### require: false

> Railsアプリのデータを触るバッチスクリプトで、バッチスクリプトでしか使わないgemがあるとしましょう。そのときに、`:require => false`をつけておくと、 
>
> ・Railsアプリ本体ではそのgemは呼ばれない
> ・スクリプトの方では手動でrequireすればよい 
>
> ということになります。 
>
> グループ指定とは軸がちがいます。

> rakeタスクを実行してcronジョブを作成するために使用されますが、通常Rails（またはRailsでない場合は他のフレームワーク）アプリケーションからは使用されません。
>
> そのため、コマンドラインから実行する必要があるがコード内で必要のないものには`:require => false`を使用できます。

この場合、gem wheneverの提供する機能（コマンド）は、アプリ内に記述するのではなく、コマンドプロンプトから実行するので、こういう形をとっているわけだ

##### *バッチスクリプト

実行させるコマンド一覧を実行順に記したファイル　コマンドプロンプトに読み込ませることで中のコマンドが順に実行される

面倒なルーティン的な動作をまとめて書いておくと便利　時間を指定して実行させたり

人が目視で確認すべき内容をここに含めるべきではない



定時実行処理の設定ファイルを作成する：

```
bundle exec wheneverize .
	create config/schedule.rb
```

ここに定時処理を記述していく形になる

定時実行させることのできる`job_type`というものが４種類ある

* command　　"/usr/bin/my_great_command"とか　bashコマンド
* runner　　　  "MyModel.some_process"とか　　　Rails内のメソッド実行
* rake　　　  　"my:rake:task"とか　　　　　　　　　rakeタスクの実行
* script　　　　　　　　　　　　　　　　　　　　　scriptの実行

`job_type`は自作することもできる

基本設定：

```
<config/schedule.rb>

# Rails.rootを使用するために必要
require File.expand_path(File.dirname(__FILE__) + "/environment")
# cronを実行する環境変数
rails_env = ENV['RAILS_ENV'] || :development
# cronを実行する環境変数をセット
set :environment, rails_env
# cronのログの吐き出し場所
set :output, "#{Rails.root}/log/cron.log"
```

そもそもwheneverはcronを元に実行するので、設定をcronへ反映させてやる必要がある

で、それさえもwheneverでコマンドを提供してくれている

```
bundle exec whenever --update-crontab
[write] crontab file updated
```

なんかPCの権限がどうとか聞かれるかも。「おｋ」する

で、反映されたことをcron側のコマンドで確認することができる

```
crontab -l
```

BeginからEndの間に書かれたタスクが、updateによって、cronへ追加されたジョブ

だからここにwheneverで設定したタスクが書いてあればokのはず

最後にwheneverでcronに反映しているかをチェックする

```
bundle exec whenever
[add] writing `./config/schedule.rb'
[done] wheneverized!
この記述が出ればひとまずok？
```

このとき、以下のメッセージが出る。意味は「上記はcron構文に変換されたスケジュールファイルです」

```
Above is your schedule file converted to cron syntax; your crontab file was not updated.
```



***

#### 
