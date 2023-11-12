# Git

[TOC]

## 简介

- 分布式版本控制工具



## 安装与常用命令

- ls/ll 查看当前目录
- cat 查看文件内容
- touch 创建文件
- vi vi编辑器
  - i 或insert键：插入
  - esc 退出编辑模式
  - zz或:wq  保存修改并退出vi
  - :w 保存



## 本地仓库



## 配置别名

- 在C盘用户目录下的.bashrc文件中
- eg.  **alias** ll='ls -al' 



## 基础操作

- git add 工作 -> 暂存
- git commit -m"注释内容"  暂存 -> 本地仓库

![image-20231012194850070](http://123.249.77.122/wp-content/uploads/2023/10/c92358c37fa6eabeb3bd5823fb93596.png)

- git log [option]  查看提交日志
  - --all 显示所有分支
  - --pretty=online 将提交信息显示为一行
  - --abbrev-commit 使输出的commitId更加简短
  - --graph 以图的形式显示
  - 上面关于log这一堆指令已经配置了别名，用git-log就行
- git reset --hard commitID 版本切换
- git reflog 记录前面所有的操作及操作后的commitId （可查看已经删除的记录）
- 添加文件到忽略列表
  - 创建一个 .gitignore
  - 然后把不想给git管理的文件名输进去（可以使用一些匹配符）

## 分支

-  查看分支
  - git branch
  - git branch -vv 显示更详细的信息
- 新建分支
  - git branch 分支名 
- **切换分支**
  - git checkout 分支名 
- 创建并切换分支
  - git checkout -b 分支名

- **合并分支**（将指定分支合并到当前分支）
  - git merge 分支名
- 删除分支
  - git branch -d 分支名 （删除时做各种检查）
  - git branch -D 分支名 （强制删除）
  - git push origin --delete 分支名 （删除远端分支）
  
- 解决冲突
  - 合并时若有冲突，两个分支的内容都会被写到文件中，需要手动处理，然后add commit

### 分支规范

- master （生产）分支：线上分支，主分支
- develop （开发）分支：master派生，一般不删
- feature/xxxx develop派生，可删
- hotfix/xxxx master派生，一般作为修复bug用，可删

![image-20231012215700770](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184254829-1461049498.png)

## 仓库托管

### 配置ssh公钥

- 生成公钥
  - ssh-keygen -t rsa
- 获取公钥
  - cat ~/.ssh/id_rsa.pub
- 在gitee设置中配置公钥
- 验证
  - ssh -T git@gitee.com

### 常用指令

- 添加远程仓库
  -  git remote add 远端名称 仓库ssh
- 查看
  - git remote
- 推送（origin是前面起的远端名，可改）
  - git push origin master

- 完整版推送

  - git push [-f] [--set-upstream\][本地分支名\]:[远端分支名\]
  - -f 强制推送覆盖

  - --set-upstream 推送并和远端分支建立关联，建立关联后直接git push即可

- 克隆

  - git clone ssh地址 文件夹名

- 抓取：只抓不合并

  - git fetch [remote name\][branch name\]
  - 然后 git merge合并

- 拉取

  - git pull [remote name\][branch name\]
  - 相当于fetch + merge



## git在Idea中的使用

[【Git】在IDEA中使用Git_idea git如何使用-CSDN博客](https://blog.csdn.net/qq_62592360/article/details/128949057?ops_request_misc=&request_id=&biz_id=102&utm_term=git在idea中的使用&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-3-128949057.nonecase&spm=1018.2226.3001.4187)



## 错误记录

### github不记录贡献值？

- 查看邮箱，用户名是否对应
- 把master改为默认分支（改完这个就有显示了）
