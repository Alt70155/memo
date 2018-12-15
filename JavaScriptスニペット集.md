# JavaScriptスニペット集

### ・サイト全体のフォントを**bold**にする
```JavaScript
document.body.style.fontWeight = "bold";
```

### ・selectの要素を選択する
```HTML
<select class="" name="">
  <option value="a">a</option>
  <option value="b">b</option>
  <option value="c">c</option>
</select>
```

上の要素に対してaを選択する

```JavaScript
document.querySelectorAll('select').forEach(elem => {
  elem.children[0].selected = true;
});
//選択したい要素を指定して表示する
document.querySelectorAll('option').forEach(elem => {
  if(elem.value === 'c') {
    elem.selected = true;
  }
});
```

### 正規表現・例
(クープ、[半角空白or全角空白or・]、?(?で区切り無しの場合も含む)、バ、[ゲorケ]、ット) <br>
上の条件に当たる文字列を取り出す。 <br>
[半角空白or全角空白or・]これは任意の1文字になるので「.」で書き直せる
```JavaScript
const text = "クープバゲットのパンは美味しかった。\n今日はクープ バゲットさんに行きました。\nクープ　バゲットのパンは最高。\nジャムおじさんのパン、ジャムが入ってた。\nまた行きたいです。クープ・バゲット。\nクープ・バケットのパン、売り切れだった（><）";
const arr = text.split(/\n/).map(x => {
  if(x.match(/クープ.?バ[ゲケ]ット/)) {
    return x;
  }
});
document.write(arr);
```
