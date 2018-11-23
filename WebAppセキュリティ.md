# Webアプリケーションセキュリティ

### 入力値の文字エンコーディングp118
・入力値検証はアプリケーション仕様に基づいて行う
・文字エンコーディングの検証
・制御文字を含む文字種の検証
・文字数の検証
・数値の最小・最大値の検証

実施は以下の手順で行う
・設計段階で各パラメータの文字種および最大文字数、最小値・最大値を仕様として決める
・設計段階で入力値検証の実装方針を決める
・開発段階では仕様に従い入力値検証を実装

### XSS対策の基本
HTMLのinputに対し、
・「＜」と「＆」をエスケープする
・属性値については、ダブルクォートで囲って、「＜」と「"」と「＆」をエスケープする
phpでは、htmlのエスケープ処理にはhtmlspecialchars関数がある

### XSSに対する保険的対策p136
#### X-XSS-Protectionの使用
ブラウザのXSSフィルタというセキュリティ機能を有効にする。
Apacheの設定例(mod_headersが必要)
```bash
Header always append X-XSS-Protection: 1; mode=block
```

#### クッキーにHttpOnly属性を付与する
JavaScriptからのクッキー読み出しを禁止する
PHPの場合、php.iniに設定を追加

```php
session.cookie_httponly = on
```

## まとめ
**必須対策(個別の対策)**

- HTMLの要素内容 htmlspecialchars関数によりエスケープ
- 属性値 htmlspecialchars関数によりエスケープしてダブルクォートで囲む

**必須対策(共通対策)**

- HTTPレスポンスに文字エンコーディングを明示する

**保険的対策**

- X-XSS-Protection レスポンスヘッダの使用
- 入力値検証
- クッキーにHttpOnly属性を付与
- TRACEメソッドの無効化

### URLを生成する場合の対策
URLをプログラムで生成する場合、httpとhttpsスキームのみを許可するようにチェックする必要がある。また、通ったURLは属性値としてHTMLエスケープする必要がある。
- http: または https: で始まる絶対URL
- スラッシュ「/」で始まる相対URL(絶対パス参照)

これをPHPでチェックする関数の実装例

```js
function check_url($url) {
  if (preg_match('/\Ahttps?:/', $url) || preg_match('/\A\//', $url)) {
    return true;
  } else {
    return false;
  }
}
```

引数で与えた文字列が「http:」あるいは「https:」あるいは「/」で始まっているかをtrue falseで判定

### JavaScriptの文字列リテラルの動的生成の対策
#### script要素の外部でパラメータを定義して、JavaScriptから参照する方法
カスタムデータ属性を利用して、通常のHTMLエスケープによりXSS対策が可能になる。
```html
<!DOCTYPE html>
<html lang="ja" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title></title>
  </head>
  <body>
    <!--phpからクエリ―文字列を受け取り、nameに格納して出力。
    それをカスタムデータ属性のdata-nameに渡す-->
    <div id="name" data-name="<?php echo htmlspecialchars($_GET['name'], ENT_COMPAT, 'UTF-8'); ?>"></div>
    <script>
    const div = document.getElementById('name');
    const txt = div.dataset.name;
    div.textContent = `${txt}の文字数は${txt.length}文字です`;
    </script>
  </body>
</html>
```
