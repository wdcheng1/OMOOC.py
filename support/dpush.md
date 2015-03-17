# double push
~ 是什么以及为什么

## 现象

- [gitbook 双推技巧](https://github.com/OpenMindClub/OMOOC.py/wiki/gitbook_double_push)
- [gitbook 多人合作技巧](https://github.com/OpenMindClub/OMOOC.py/wiki/collaborate)

## 解决
~ 本质上由于 gitbook `NoZuoNoDie` 的行径,导致以往的自然流程失效:

- 注册 github , 开辟仓库
- 注册 gitbook , 创建图书, 绑定 github 仓库
- 从 github clone 图书仓库
- 在本地编辑图书
- push github
    + github 自动通知 gitbook
    + gitbook 自动去 github 同步文本
    + gitbook 自动编译发布图书

### 宏观上

已知:

- gitbook 的图书仓库和 github 源内容仓库无法自动同步
- gitbook 有可接触的 git 仓库
- gitbook 的图书仓库,没有 github 仓库那些丰富的管理/分享/传播功能

目标:

- 希望原先图书管理流程不变
- github 仓库专注图书的管理/编辑/协同...
- gitbook 专注图书的编译/发布

所以:

- 人工解决 github 和 gitbook 仓库不同步问题
- 同时/接连 将图书内容 push 到两个仓库就好!


### 技术上

`github`

- 使用 ssh 认证, 不用口令
- 通过 git/ssh 协议高速交互

`gitbook`

- 只能使用 https 协调交互
- 只能使用 明文 帐号+口令 认证
- 如果,图书发布 URI 是:
    + `https://www.gitbook.com/book/[帐号]/[图书名]/details`
    + 则对应的仓库应该是:
    + `https://git.gitbook.com/[帐号]/[图书名].git`

技巧:

- 和 svn/hg 等等版本管理仓库类似
- git 仓库也支持在配置中明文保存口令,以免反复输入
- 形如:
    + `url = https://用户名:口令@git.gitbook.com/帐号/[图书名].git`


### 操作上

- 已经创建好 github 图书仓库, gitbook 图书实例
- 并将 github 仓库 clone 到本地: `/path/2/[图书名]`
- 则应该可以看到 `/path/2/[图书名]/.git/config` 有以下配置声明


```
[remote "origin"]
    url = git@github.com:OpenMindClub/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

- 那么就可以手工增订为:

```
[remote "book"]
    url = https://git.gitbook.com/[帐号]/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
[remote "hub"]
    url = git@github.com:OpenMindClub/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
[remote "origin"]
    url = git@github.com:OpenMindClub/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

- 回到仓库根目录,测试:

```
$ git remote -v
book    https://git.gitbook.com/[帐号]/[图书名].git (push)
book    https://git.gitbook.com/[帐号]/[图书名].git (fetch)
hub git@github.com:OpenMindClub/[图书名].git (push)
hub git@github.com:OpenMindClub/[图书名].git (fetch)
origin  git@github.com:OpenMindClub/[图书名].git (push)
```


- 配置明文口令,以便不用输入

```
[remote "book"]
    url = https://[用户名]:[口令]@git.gitbook.com/[帐号]/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
```


- `注意:` 隐藏约定 ~ `口令中不能包含 @` ,否则,你懂的...


#### 手工双推

那么,俺就可以用两次 git 操作,完成两个仓库的内容发送了:

```
$ git pu hub
Counting objects: 18, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (16/16), 3.29 KiB | 0 bytes/s, done.
Total 16 (delta 3), reused 0 (delta 0)
To git@github.com:OpenMindClub/[图书名].git
   8c3c8b6..f27428a  master -> master

$ git pu book
Counting objects: 20, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (16/16), 3.29 KiB | 0 bytes/s, done.
Total 16 (delta 3), reused 0 (delta 0)
To https://[用户名]:[口令]@git.gitbook.com/[帐号]/[图书名].git
   8c3c8b6..f27428a  master -> master

```

#### 自动双推

经过文档研究,发现,其实, git 可以任性的识别仓库配置中,多个 uri 的,那么增强配置为

```
...
[remote "origin"]
    url = git@github.com:OpenMindClub/[图书名].git
    url = https://[用户名]:[口令]@git.gitbook.com/[帐号]/[图书名].git
    fetch = +refs/heads/*:refs/remotes/origin/*
```

再进行默认推送, 就发现,自动完成了两个仓库的分别 push:



