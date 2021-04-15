#### rake(TIL)



##### rakeの意義

* 何かしらのデータ連携
* データベースのバックアップ定期実行
* 定期的なデータの更新、削除
* コード分析ツールの実行（リントとか）
* 任意のタイミングでuserにメールを送る

##### 作り方

1. rakeタスク用のファイルを作成する

   `$ bundle exec rails generate task greeting`

2. rakeファイル内に、rakeタスクを定義する

   ```
   lib/tasks/greeting.rake
   
   namespace :greeting do
   	desc(説明) "下記のタスクが何をするものか"
   	task :hello_user :environment do
   		user = User.find(:first)
   		puts "hello #{user.name}"
   	end
   end
   ```

   ファイル名と同じ名前空間を作り、その中でタスクを定義する

   `environment do` は事前タスク実行。railsのアプリのコードを読み込んで準備をしている

3. 実行

   `$ rake greeting:hello_user => hello ユーザー`

##### 
