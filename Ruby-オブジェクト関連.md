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

#### module_functionメソッド
ミックスインとしても、モジュールの特異メソッドとしても使えるようにする設定

```Ruby
module Loggable
  def log(text)
    puts "[LOG] #{text}"
  end

  module_function :log
end

#モジュールの特異メソッドとしての呼び出し
Loggable.log('hello')

class Product
  include Loggable

  def title
    #includeしたLoggableモジュールのlogメソッドを呼び出す
    log 'title is called'
        'A great movie'
  end
end

#ミックスインとしてlogメソッドを呼び出す
pro = Product.new
pro.title
```
このように、両方でも使えるように設定したlogメソッドのようなことをモジュール関数と呼ぶ

### コンポジション

```Ruby
class Bicycle
  attr_reader :size, :parts

  def initialize(args={})
    @size  = args[:size],
    @parts = args[:parts]
  end

  def spares
    parts.spares
  end
end

require 'forwardable'
class Parts
  extend Forwardable
  def_delegators :@parts, :size, :each
  include Enumerable

  def initialize(parts)
    @parts = parts
  end

  def spares
    # part.needs_spareがtrueのみ抽出
    # self.select { |part| part.needs_spare }
    select(&:needs_spare)
  end
end

require 'ostruct'
module PartsFactory
  # buildというクラスメソッド
  def self.build(config, parts_class = Parts)
    parts_class.new(
      config.map { |part_config| create_part(part_config) }
    )
  end

  def self.create_part(part_config)
    OpenStruct.new(
      name:        part_config[0],
      description: part_config[1],
      needs_spare: part_config.fetch(2, true)
    )
  end
end
# PartsFactoryがやってること
# Partsの引数にPartインスタンスを格納した配列を渡す
# Parts.new([
#   OpenStruct.new(name: 'chain', description: '10-speed'),
#   OpenStruct.new(name: 'tire_size', description: '23'),
#   OpenStruct.new(name: 'tape_color', description: 'red')
# ])

# chain =
#   Part.new(name: 'chain', description: '10-speed')
#
# road_tire =
#   Part.new(name: 'tire_size', description: '23')
#
# tape =
#   Part.new(name: 'tape_color', description: 'red')
#
# mountain_tire =
#   Part.new(name: 'tire_size', description: '2.1')
#
# rear_shock =
#   Part.new(name: 'rear_shock', description: 'Fox')
#
# front_shock =
#   Part.new(
#     name: 'front_shock',
#     description: 'Manitou',
#     needs_spare: false
#   )

# road_bike_parts     = Parts.new([chain,
#                                  road_tire,
#                                  tape])
# mountain_bike_parts = Parts.new([chain,
#                                  mountain_tire,
#                                  front_shock,
#                                  rear_shock])

#Partsの引数にPartインスタンスを格納した配列を渡す
# road_bike_parts_matome = Parts.new([
#   Part.new(name: 'chain', description: '10-speed'),
#   Part.new(name: 'tire_size', description: '23'),
#   Part.new(name: 'tape_color', description: 'red')
# ])
#
# @road_bike =
#   Bicycle.new(
#     size: 'L',
#     parts: road_bike_parts
#   )
#
# @mountain_bike =
#   Bicycle.new(
#     size: 'L',
#     parts: mountain_bike_parts
#   )

# 第三引数はスペアを必要とするかどうか・省略した場合は必要
# これらは自転車の設計図
road_config = [
  ['chain',      '10-speed'],
  ['tire_size',  '23'],
  ['tape_color', 'red']
]

mountain_config = [
  ['chain',       '10-speed'],
  ['tire_size',   '2.1'],
  ['front_shock', 'Manitou', false],
  ['rear_shock',  'Fox']
]

recumbent_config = [
  ['chain',     '9-speed'],
  ['tire_size', '28'],
  ['flag',      'tall and orange']
]

p road_parts = PartsFactory.build(road_config)
p recumbent_parts = PartsFactory.build(recumbent_config)
```

### privateメソッドについて

privateメソッドを呼び出すときは、レシーバは指定できない(self)

```Ruby
class Sample

  def func1
    private_func #通常呼び出し
  end

  def func2
    self.private_func #あえてレシーバ(self)を明示して指定
  end

  private
    def private_func
      p "Hello"
    end
  #private
end

s1 = Sample.new
s1.func1 #=> Hello
s1.func2 #=> NoMethodError
```

他にも、自分(self)以外のオブジェクトのメソッドを呼び出すには、レシーバを指定する必要がある。(当たり前ではある)

```Ruby

class Sample2

  def s2_func1
    s1 = Sample.new
    s1.func1 #通常呼び出し
  end

  def s2_func2
    s1 = Sample.new
    func1
  end

end

s2 = Sample2.new
s2.s2_func1
s2.s2_func2

```
