# 直前のコミットの取り消し
`git reset --soft HEAD~1`

# 履歴を消して完全新規のリポジトリにする（１コミットだけにする）
```
rm -rf .git
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin url
git push -u origin main
```
