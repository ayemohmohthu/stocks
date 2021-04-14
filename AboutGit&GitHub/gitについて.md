## git作成
  ▼ git init
## ファイル作成
  cat >> readme.md
  ||
  echo "# local git" > sample.md
## save & exit
  ▼ command+d
## gitにファイル追加
  ▼ git add readme.md
## コミット
```
  git commit -a -m "comment!"
  git commit -m "comment!"
  [optional]
   -m         : コミットメッセージを１行以内に納める
   --amend -m : 一つ前のコミットと統合する
```
## リモートに共有
### config設定
```
  git config --global user.name "ayemohmohthu"
  git config --global user.email "ayemohmohthu@seattleconsulting.co.jp"
```
### remote追加
  ▼ git remote add origin https://github.com/ayemohmohthu/secondGit.git
### origin already existsの場合
```
  git remote rm origin
  を実行後リモートをもう一回追加できる。
```
### push
```
  git push -u origin master
  上記でerror: failed to push some refs to 'https://github.com/ayemohmohthu/localGitTest.git'が発生したので
  git push origin master --forceで実行した。

  git push origin implement-app-base
```
### branch作成
  ▼ git checkout -b branch名
### branch checkout
  ▼ git checkout branch名
### git condition
  ▼ git status
### branch delete
  ▼ git branch -D branch名
### branch pull
  ▼ git pull origin master

### git-flow と　github-Flowの違い
##### git-Flow
  ▼ master,release,hotfixs,develop,featuresのbranchesを切ってから作業する。
##### github-Flow
  ▼ master,topicのbranches二つだけで作業をする。

#### How do I commit case-sensitive only filename changes in Git?
git mv -f OldFileNameCase newfilenamecase
git commit -m "comment"
git push origin [branch]
