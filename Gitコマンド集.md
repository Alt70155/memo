# Gitコマンド集

<!-- テンプレ -->
<!-- #### ・
```bash
``` -->

#### ・トピックブランチの作成、削除
トピックブランチとは、短期間だけ使う一時的なブランチ
```bash
#作成
git checkout -b (branch名)
#削除
git branch -d (branch名)
#トピックブランチ上の変更を破棄し、削除
git branch -D (branch名)
```

#### ・ローカルブランチの一覧表示
```bash
git branch
```

#### ・ブランチの状態確認
```bash
git status
```

#### ・マージ
masterブランチにトピックブランチの変更を追加する
```bash
git merge (branch名)
```
