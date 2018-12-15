# Ruby:オブジェクト関連について

### クラスインスタンス変数とは

```ruby
class Product
  #クラスインスタンス変数
  @name = 'Product'
  def self.name
    #クラスインスタンス変数
    @name
  end

  def initialize(name)
    #インスタンス変数
    @name = name
  end

  def name
    #インスタンス変数
    @name
  end
end

p Product.name #=> "Product"
product = Product.new('A great movie')
p product.name #=> 'A great movie'
p Product.name #=> "Product"

```

インスタンス変数はクラスをインスタンス化(クラス名.newでオブジェクトを作成)した際に、オブジェクトごとに管理される変数
一方、クラスインスタンス変数はインスタンスの作成とは無関係に、クラス自身が保持しているデータ(クラス自身のインスタンス変数)。

### ネストしたクラスの定義と参照

```ruby
class User
  class BloodType
    attr_reader :type

    def initialize(type)
      @type = type
    end
  end
end

blood_type = User::BloodType.new('B')
p blood_type.type #=> B
```

### クラスメソッドやクラス構文直下のself

```ruby
class Foo
  puts "クラス構文の直下のself:#{self}"

  def self.bar
    puts "クラスメソッド内のself:#{self}"
  end

  def baz
    puts "インスタンスメソッド内のself:#{self}"
  end
end
#=>　クラス構文の直下のself: Foo
Foo.bar #=> クラスメソッド内のself: Foo

foo = Foo.new
foo.baz #=> インスタンスメソッド内のself
```

### モジュールについて
ログを出力する処理が重複しているクラスが下にある。 <br>
これを、重複したコードをまとめようと安易に継承を使ったりしてはいけない。
「製品はユーザである」または「ユーザは製品である」という関係(is-aの関係)が成り立たないのであれば、継承の使用は避けるべきである。<br>

```Ruby
class Product
  def title
    log 'title is called.'
        'A great movie'
  end

  private

  def log(text)
    puts "[LOG] #{text}"
  end
end

class User
  def name
    log 'name is called'
        'Aice'
  end

  private

  def log(text)
    puts "[LOG] #{text}"
  end
end
```

継承は使えないが、「ログを出力する」という共通の機能は持たせたいという時にモジュールを使う。

```Ruby
module Loggable
  private
  def log(text)
    puts "[LOG] #{text}"
  end
end

class Product
  include Loggable

  def title
    log 'title is called.'
        'A great movie'
  end
end

class User
  include Loggable

  def name
    log 'name is called'
        'Aice'
  end
end

p Product.new.title
p User.new.name
```

#### モジュールをextendする
モジュールをクラスにミックスインするもうひとつの方法としてextendがある。
extendを使うと、モジュール内のメソッドをそのクラスの特異メソッド(つまりクラスメソッド)にすることができる。

```Ruby
module Loggable
  def log(text)
    puts "[LOG] #{text}"
  end
end

class Product
  extend Loggable

  def self.create_products(names)
    #logメソッドをクラスメソッド内で呼び出す
    #(つまりlogメソッド自体もクラスメソッドになっている)
    log 'create_products is called.'
  end
end

Product.create_products([])
Product.log('hello')
# p Product.new.create_products([]) エラー
```
