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
    + `https://www.gitbook.com/book/[帐号]/omooc-py/details`
    + 则对应的仓库应该是:
    + `https://git.gitbook.com/[帐号]/omooc-py.git`

技巧:

- 和 svn/hg 等等版本管理仓库类似
- git 仓库也支持在配置中明文保存口令,以免反复输入
- 形如:
    + `url = https://用户名:口令@git.gitbook.com/帐号/omooc-py.git`


### 操作上



