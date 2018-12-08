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
