## GitHub Flowの6つのルール
```
 ▼ masterブランチは常にリリース可能な状態にしておく
 ▼ かならずmasterブランチからtopicブランチを切る
   ■ git checkout -b branch名
 ▼ ブランチは定期的にPushする
   ■ git push origin branch名
 ▼ プルリクエストを使ってコードレビューをする
 　■ githubでpull requestを作成してレビューしてもらう。
 ▼ masterブランチにマージする前にコードレビューを行う
 　■ GitHubで「Files changedタブ」からコミットの内容をレビューします。
 ▼ コードレビューを通過したらすみやかにマージする。
 　■ 特に問題はないようでしたら、レビュー完了メッセージを書き込んでからマージを行う。
``` 
