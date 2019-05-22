# Git 常用命令

## 1. 初始化 `git` 创建版本库:

```bash
git init
```

## 2. 把文件添加到暂存区

```bash
# 把这个文件，提交到暂存区
git add <file>
# 把工作区内，修改过的和新创建的文件提交到暂存区，但不包括已删除文件
git add .
# 把工作区内，对已提交到暂存区的文件进行更新
git add -u
git add --update
# 把工作区内，所有提交到暂存区
git add -A
git add --all
```

## 3. 查看暂存区状态

```bash
git status
```

## 4. 删除文件

```bash
# 从版本库删除某个文件
git rm <file>
# 从暂存区删除某个文件
git rm --cache <file>
# 强行删除某个文件
git rm -f <file>
# 删除文件夹
git rm -r <folder>
```

## 5. 撤销文件修改

```bash
# 如果文件已提交到暂存区，则恢复到提交到暂存区的状态，反之恢复到最近一个版本库的状态
git checkout -- <file>
# 把暂存区和工作区的文件，恢复到最近一个版本库的状态
git reset HEAD <file>
```

## 6. 把暂存区所有文件提交到 `git` 版本库

```bash
git commit -m <message>
```

## 7. 查看文件修改内容

```bash
# 查看此文件，两次提交到暂存区的不同
git diff <file>
# 查看此文件，工作区与上次提交到版本库的不同
git diff HEAD -- <file>
```

## 8. 查看提交日志

```bash
git log
```

## 9. 切换版本

```bash
# 回到上个版本，HEAD 指代当前版本，^ 指代上一个、前一个 
git reset --hard HEAD^
# 回到某一个版本
git reset --hard <commit id>
```

## 10. 查看版本更替日志

```bash
git reflog
```

## 11. 分支

```bash
# 查看分支目录
git branch
# 创建并切换分支
git checkout -b <branch>
# 创建分支
git branch <branch>
# 切换分支
git checkout <branch>
# 合并某一分支到当前分支
git merge <branch>
# 删除分支
git branch -d <branch>
# 强行删除分支
git branch -D <branch>
```

## 12. 标签

```bash
# 查看当前分支标签
git tag
# 为当前分支添加标签
git tag <tag>
# 为指定标签附加信息，如果没有此标签则创建此标签
git tag -a <tag> -m <message>
# 删除标签
git tag -d <tag>
```
