refers:
- [https://www.jianshu.com/p/4a8f4af4e803](https://www.jianshu.com/p/4a8f4af4e803)


## `merge`
`git checkout main`
`git merge feature`
生成一条merge commit
![](../../assets/Pasted%20image%2020250228142647.png)


### `fast-forward`
feature中已经有完整的main记录，不会有merge commit
![](../../assets/Pasted%20image%2020250228142742.png)

## `rebase`
`git checkout feature`
`git rebase main`
![](assets/Pasted%20image%2020250228143000.png)



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