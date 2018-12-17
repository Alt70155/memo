# Rubyスニペット集

### ・正規表現

```Ruby
text = <<-TEXT
クープバゲットのパンは美味しかった。
今日はクープ バゲットさんに行きました。
クープ　バゲットのパンは最高。
ジャムおじさんのパン、ジャムが入ってた。
また行きたいです。クープ・バゲット。
クープ・バケットのパン、売り切れだった（><
TEXT
# \d数字2文字から5文字 ハイフン 数字1~4文字　ハイフン 数字4文字
text.scan(/\d{2,5}-\d{1,4}-\d{4}/)
# \d数字2~5文字 ハイフンorカッコ 数字1~4文字　ハイフンorカッコ 数字4文字
text.scan(/0[1-9]\d{0,3}[-(]\d{1,4}[-)]\d{4}/)
#クープ、[半角空白or全角空白or・]、?(?で区切り無しの場合も含む)、バ、[ゲorケ]、ット
text.split(/\n/).grep(/クープ[ 　・]?バ[ゲケ]ット/)
#[半角空白or全角空白or・]これは任意の1文字になるので「.」で書き直せる
text.split(/\n/).grep(/クープ.?バ[ゲケ]ット/)
```
grepとは各要素に対して「引数obj === 要素」を試し、その結果が真だった要素を集めて配列にして返す
```Ruby
p ["cat", 123, "#dog", "mouse"].grep(String)
# "cat" "#dog" "mouse"
```
#### キャプチャする

・\wは[a-zA-Z0-9_] <br>
・キャプチャする必要がない()は(?:)を追加 <br>
・(?: selected)? 最後のハテナは前の条件とのOR条件 例：(ABC)? =「文字列ABCがあり、または無し」の意味 <br>
・「.+」= 任意の文字が1文字以上 <br>
・「.\*」= 任意の文字が0文字以上 <br>
```Ruby
html = <<-HTML
<select name="game_console">
<option value="none"></option>
<option value="wii_u" selected>Wii U</option>
<option value="ps4">プレステ4</option>
<option value="gb">ゲームボーイ</option>
</select>
HTML

replaced = html.gsub(/<option value="([\w]+)"(?: selected)?>(.*)<\/option>/, '\1, \2')
puts replaced
```

#### :hoge => をhoge:に置き換える
```Ruby
def convert_hash_syntax(old_syntax)
  p old_syntax.gsub(/:(\w+) *=> */, '\1: ')
end

old_syntax = <<TEXT
{
  :name => 'Alice',
  :age=>20,
  :gender  =>  :female
}
TEXT
convert_hash_syntax(old_syntax)
```

### 例外処理
例外発生時にもう一度処理をやり直すretry
```Ruby
retry_ct = 0
begin
  puts '処理の開始'
  1 / 0
rescue
  retry_ct += 1
  puts '処理の失敗・・・'
  if retry_ct <= 3
    puts "retryします (#{retry_ct}回目)"
    sleep(1)
    retry
  else
    puts 'retryに失敗しました'
  end
end
```

#### 例外処理の使い所
例えば、100人に一斉メールを送るという処理では、1人のエラーのせいで99人の処理を止めるというのは合理的ではない。
そういう場合に、1人のエラーをrescueして、最後のユーザまでメール送信処理を続行する。
その際、エラーメッセージをできるだけログに出力しておく。
```Ruby
user_mail_adress = [1,2,3,4,5,0,4,3,1]

def send_mail_to(address)
  #メールを送る処理だとする
  #メールアドレスにおかしな値があったという想定で、エラーを起こす
  #今回の例では0がおかしな値という例で10 / 0の時のzero division errorを発生させる
  puts 10 / address
end

user_mail_adress.each do |address|
  begin
    send_mail_to(address)
  rescue => e
    puts "#{e.class}: #{e.message}: #{puts e.backtrace}"
  end
  #resucueしないと、0が与えられた時点で処理が止まる。
end
```

#### 予期しない条件は以上終了させる
case文で条件分岐を作る場合、どんなパターンが事前にわかっているケースが多い。
そういう場合はwhenで想定可能なパターンを全て網羅し、elseで「想定外のパターン」として例外を発生させることを検討する。
elseを用意しないパターン
```Ruby
def currency_of(country)
  case country
  when :japan
    'yen'
  when :us
    'dollar'
  when :india
    'rupee'
  end
end

curr = currency_of(:itary)
curr.upcase #=> nilなのでNoMethodError
#別のメソッドを呼び出すまでエラーが分からない
```

elseに入ったら例外を発生させる良い例のパターン
```Ruby
def currency_of(country)
  case country
  when :japan
    'yen'
  when :us
    'dollar'
  when :india
    'rupee'
  else
    raise ArgumentError, "無効な国名です。 #{country}"
  end
end

currency_of(:itary)
```

#### begin/endを省略するrescue修飾子
```Ruby
require 'date'

def to_date(string)
  begin
    #文字列のパースを試みる
    Date.parse(string)
  rescue ArgumentError
    #パースできあに場合はnilを返す
    nil
  end
end

#パース可能な文字列を渡す
p to_date('2017-01-01')
#パース不可能な文字列
p to_date('aaabbbb')
```

これをrescueで置き換える
```Ruby
require 'date'

def to_date(string)
  Date.parse(string) rescue nil
end
```
これは捕捉する例外クラスを指定できない

### Proc
procオブジェクトの作り方

```Ruby
#Procオブジェクト
Proc.new { |a, b| a * b }
#procメソッド
proc { |a, b| a * b }
#->構文 ()は省略可能、引数がなければ全て省略可能
->(a, b) { a + b }
#lambdaメソッド
lambda { |a, b| a * b }
```

下二つのラムダはProc.newより引数のチェックが厳密。

Proc.newの場合
```Ruby
add_proc = Proc.new { |a, b| puts a.to_i + b.to_i }
#Proc.newは引数が１つまたは３つでも呼び出し可能
add_proc.call(10) #=> 10
add_proc.call(10, 30, 40) #=> 40
```

lambdaの場合
```Ruby
add_lambda = -> (a, b) { |a, b| a.to_i + b.to_i }
add_lambda.call(10) #=> ArgumentsError
add_lambda.call(10, 30, 40) #=> ArgumentsError
```

#### 例題
```Ruby
module Effects
  #特異メソッド(クラスメソッド的なもの)
  def self.reverse
    ->(words) do
      #スペースで分解>逆順に並べ替え>スペースで連結
      words.split(' ').map(&:reverse).join(' ')
    end
  end

  def self.echo(rate)
    ->(words) do
      #スペースならそのまま返す
      #スペース以外ならその文字を指定された回数だけ繰り返す
      words.chars.map { |c| c == ' ' ? c : c * rate }.join
    end
  end

  def self.loud(level)
    ->(words) do
      #スペースで分割>大文字変換と!の付与>スペースで連結
      words.split(' ').map { |word| word.upcase + '!' * level }.join(' ')
    end
  end
end


class WordSynth
  def initialize
    @effects = []
  end

  #インスタンスメソッド
  def add_effect(effect)
    @effects << effect
  end

  def play(original_words)
    @effects.inject(original_words) do |words, effect|
      effect.call(words)
    end
  end
end

syn = WordSynth.new
syn.add_effect(Effects.reverse) #Effects.reverseはProcオブジェクト
#単体で呼び出す場合は、Effects.reverseはProcなので引数をつけてcallする
p Effects.reverse.call('Ruby is fun!')
```
