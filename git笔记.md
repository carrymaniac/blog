## 开辟分支进行操作
```git
git branch newb
git checkout newb
```

## 上传本地分支到远程分支
```
git push origin newb
```

## 合并到master
```
# 切换到master
git checkout master
# 将远程master的数据拉取下来
git pull origin master
# 合并
git merge newb
# 查看状态
git status

```