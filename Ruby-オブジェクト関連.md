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
