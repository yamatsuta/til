#### Rails.root.join()

まずPathnameクラスがある

パス名をオブジェクトらしく扱うためのクラス

```
[1] pry(main)> Rails.root.class
=> Pathname
[2] pry(main)> Rails.root
=> #<Pathname:/Users/cothmeil/workspace/835_yamatsuta_runteq_learning_advanced>
[3] pry(main)> Rails.root.dirname
=> #<Pathname:/Users/cothmeil/workspace>
```

Pathname#join

連結したいディレクトリ名やファイル名を文字列で与える

```
require "pathname"

path0 = Pathname("/usr")                # Pathname:/usr
path0 = path0.join("bin/ruby")          # Pathname:/usr/bin/ruby
```

Pthnameクラスのインスタンスにjoinメソッドで、引数にパスの文字列（頭が/じゃまずい）を与えると、連結されたPathnameクラスのインスタンスに更新される

```
  def user_not_authorized
    render file: Rails.root.join('public/403.html'), status: :forbidden, layout: false, content_type: 'text/html'
  end
```

render は表示方法を指定できる

`file: ファイルパス`で指定できる。`Rails.root.join('public/403.html')`がファイルパスであることの証左



***

