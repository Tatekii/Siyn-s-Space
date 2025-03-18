refers:
- [https://www.jianshu.com/p/4a8f4af4e803](https://www.jianshu.com/p/4a8f4af4e803)


## `merge`
git merge 用于将一个分支的更改合并到当前分支，并创建一个新的合并提交（merge commit）。

**特点**
- **保留原始提交历史**，不会修改提交顺序。
- **产生一个额外的合并提交**（merge commit）。

**示例**
`git checkout main`
`git merge feature`
生成yi tiao
![](../../assets/Pasted%20image%2020250228142647.png)


### `fast-forward`
注意：feature中已经有完整的main记录，不会有merge commit
![](../../assets/Pasted%20image%2020250228142742.png)

## `rebase`
git rebase 通过重新应用提交（replay commits）的方式，把一个分支的更改整合到另一个分支，**不会创建合并提交**。

**特点**
- **不会产生额外的合并提交**，提交历史更加线性清晰。
- **修改提交历史**（可能导致强推 git push --force）。
- **适用于代码整理**，如 feature 分支更新 main 的最新代码，而不产生额外的合并提交。


**示例**
`git checkout feature`
`git rebase main`
rebase 使得 feature 分支的提交在 main 分支之后，就像是它们刚刚创建的一样。
![](../../assets/Pasted%20image%2020250228143000.png)



### `rebase合并commit

#### interactive
- pick：保留该commit（缩写:p）
- reword：保留该commit，但我需要修改该commit的注释（缩写:r）
- edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
- squash：将该commit和前一个commit合并（缩写:s）
- fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
- exec：执行shell命令（缩写:x）
- drop：我要丢弃该commit（缩写:d）

```git 
[feature] A-B-C-D
[master]  A

git checkou feature
git rebase -i [startpoint] [endpoint{default HEAD}]


[feature] A-E
[master]  A

```

## `cherry-pick`
```git
[feature] A-B-C-D
[master]  A

git checkou master
git cherry-pick C

[feature] A-B-C-D
[master]  A-C
```

## `.gitignore`配置

- [https://github.com/github/gitignore](https://github.com/github/gitignore)

- [https://www.gitignore.io](https://www.gitignore.io)

- 新增被追踪的ignore文件

```bash

# 清除之前的快照

git rm --cached -r .

# 更新快照

git add .

```