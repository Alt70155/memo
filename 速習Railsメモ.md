# 速習Railsメモ

### simple_format
simple_formatは改行コードなどを変換してくれる。
simple_formatはデフォルトでエリアを<p>で囲い、テキストに含まれる一部の危険なHTMLタグを取り除く。(sanitize)
sanitizeオプションをfalseにすると、hメソッドを用いてHTMLエスケープを行う。
wrapper_tagを指定するとデフォルトのタグを変更できる。

```Ruby
simple_format( h(@task.description),
               {},
               sanitize: false,
               wrapper_tag: "div" )
```

### task_path(task)
このリンクはtasks/[タスクのid]となる。
単にtaskと書くこともできる。この場合はrailsが推測してURLが生成される。
ハッシュで{id: タスクのid}としてもOK

### params[:id]
params[:id]はリクエストパラメータ(params)から得られるid、つまりリクエストされたURL"tasks/[タスクのid]"の部分が格納される

### render partial: 'form', locals: { task: @task }
localsオプションを使うと、パーシャル内のローカル変数を設定できる。
locals: { task: @task }は、インスタンス変数@taskを、パーシャル内のローカル変数taskとして渡すという意味になる。
