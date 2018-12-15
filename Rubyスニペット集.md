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
