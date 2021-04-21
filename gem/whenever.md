#### whenever gem(TIL)

cronを書いたり実行する際に、よりクリアで洗練されたruby構文を提供するgem

```
gem 'whenever', require: false
```

```
bundle exec wheneverize .
```

config/schedule.rbファイルが作られる

rakeタスクを一時間ごとに実行させる処理をかく

```
require File.expand_path(File.dirname(__FILE__) + "/environment")

rails_env = ENV['RAILS_ENV'] || :development

set :environment, rails_env

set :output, "#{Rails.root}/log/cron"

every 1.minute do
  rake 'status_task:update_status_task'
end

```



bundle exec whenever  設定した処理を確認

bundle exec whenever --update-crontab  #=> crontabを更新

RAILS_ENV=development bundle exec whenever --update-crontab　#=> wheneverの設定を更新

bundle exec rake status_task:update_status_task　#=> rakeタスクが実行できているかを確認

crontab -l  #=> 設定されているcronを見る

