## `merge`
`git checkout main`
`git merge feature`
生成一条merge commit
![[Pasted image 20250228142647.png]]

### `fast-forward`
feature中已经有完整的main记录，不会有merge commit
![[Pasted image 20250228142742.png]]

## `rebase`
`git checkout feature`
`git rebase main`
![[Pasted image 20250228143000.png]]



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