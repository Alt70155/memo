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

### グラデーション

```HTML
<button type="button" name="button" class="button">カラー変更</button>
<div class="rectangle"></div>
```

```CSS
.rectangle {
  width:  800px;
  height: 400px;
  --start: hsl(100, 100%, 50%);
  --end:   hsl(1900, 100%, 50%);
  background-image: linear-gradient(-135deg, var(--start), var(--end));
  border: 1px solid black;
}
```

```JavaScript
"use strict";

const rectangle = document.querySelector('.rectangle');
let ct = 0;
let isCountChange = true;


// ボタンをクリックするたびに、長方形のグラデーション色を変える
const onClickButton = () => {
  //0~360の間のランダムな数を取得する
  const randomHue = Math.trunc(Math.random() * 360);
  //グラデーションの開始色と終了色を決定
  const randomColorStart = `hsl(${ct}, 100%, 50%)`;
  const randomColorEnd   = `hsl(${1000 - ct}, 100%, 50%)`;

  //長方形のグラデーションのための変数(--startと--end)を変更
  rectangle.style.setProperty('--start', randomColorStart);
  rectangle.style.setProperty('--end',   randomColorEnd);

  if (ct >= 1000) {
    isCountChange = false;
  } else if (ct <= -1000) {
    isCountChange = true;
  }
  isCountChange ? ct+=2 : ct-=2;
}

// ボタンをクリックしたらonClickButton()を実行
// document.querySelector('.button').addEventListener('click', onClickButton);
setInterval(onClickButton, 40);
```

### cryptoを用いた乱数生成

```JavaScript
//ランダムな整数(16bit符号なし)が10個格納された配列を生成する
// const randomArray1 = crypt.getRandomValues(new Unit16Array(10));
const rand = x => {
  return crypto.getRandomValues(new Uint32Array(x)).join('');
}

rand1 = rand(5);
rand2 = rand(10);
console.log(rand1);
console.log(rand2);
```

### フェードアウト
container下のalertをフェードアウトさせる

```JavaScript
setTimeout(alertDelete, 2500)

function alertDelete () {
  const notice = document.querySelector('.alert')
  let fadeOut
  if (notice !== null) {
    let opacityValue = 1

    fadeOut = function fadeOutExp () {
      opacityValue -= 0.04
      notice.style.opacity = `${opacityValue}`
      if (opacityValue <= 0) {
        clearInterval(id)
        // document.querySelector('.container').removeChild(notice)
        // display: none;のほうが速度とかの面で良い？
        document.querySelector('.alert').style.display = 'none'
      }
    }

    const id = setInterval(fadeOut, 50)
  }
}

```
