---
title: github ssh-agent 配置
date: 2024-11-14
author: 付辉

---

github 上增加 SSH 的时候提供了两个选项，据我观察，它们会影响 git push 的执行效果，尤其是本地有两个 github 账号的情况。我的解决办法是，两个选项都做了配置。

![SSH授权](./images/ssh.png)

最近注册了一个新的 github 账户 i-neojos，本地还有之前注册的 github 账号 think-next，主要是感觉原账号下的工程比较杂乱，就决定重新注册一个新的，感觉就和遇到问题重启电脑一样爽！新账号的 git 管理非常顺利，git push 很顺畅，春风得意马蹄疾，一点就能push代码。

后知后觉，我在 think-next 的仓库下提交代码时会触发了下面的这误提示，这个仓库的代码不是 i-neojos 账户的，错误的提示很直白，但账号怎么就错乱了呢？

```
➜  githubsi.github.io git:(master) git push
ERROR: Permission to think-next/githubsi.github.io.git denied to i-neojos.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

我快速回忆+反思注册 i-neojos 账户的过程，其中，SSH 按照下面的官方文件做了配置，这个文档介绍的非常清晰简单，我反复看了3遍就看懂了。关于 ssh-agent 代理，很久之前压根就没有这个配置流程，好在按照命令执行就行啦。

> [# Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)


另外一点是 2FA 验证，很早之前 github 建议过我加强安全防护，但对我而言，这样的验证意义不大，我一个 start 为 0 的有什么好担心的，但好在 github 看的起我，新账户 i-neojos 开通了 2FA 验证。

我有必要说两句2FA验证，我首先尝试手机随便上下载了一个APP，但居然是收费的，只有前3天免费，果断直接卸载。最后网上找了一圈，找到了一个浏览器插件，叫「身份验证器」，免费好用。好用其次，关键是免费啊，这里推荐给大家。

诊断问题的过程，就和维修家用电器一样，首先需要把外面的壳子打开，找个梅花锥，然后扭开一个一个螺丝。我首先在提交出问题的仓库下查看 git 的配置信息，下面就是第一的指令查看当前项目的 git 配置信息

> git config --list

我发现：配置信息中的账号的 email 和仓库确实是不匹配的。这里的 email 是 i-neojos 账号的的邮箱。如果只是账号问题不匹配，这也太好解决了，我们尝试在命令行手动修改 git 的配置信息

```
git config --list

*****省略前序******
user.email=1520107395@qq.com
pull.rebase=false
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
remote.origin.url=git@github.com:think-next/githubsi.github.io.git
remote.origin.fetch=+refs/heads/*:refs/remotes/origin/*
branch.master.remote=origin
branch.master.merge=refs/heads/master
```

执行下面的指令，手动修改当前仓库的配置信息，经过我重新查看，当前项目下确实生效了。但比较遗憾，git push 的报错并没有得到解决

```
➜  githubsi.github.io git:(master) git config user.email "2493393471@qq.com"
➜  githubsi.github.io git:(master) git config user.name "think-next"
```

我现在怀疑 ssh-agent 了，网上说 ssh-agent 是一个用户级别的守护进程，用于设置一个对解密的私钥进行高速缓存的环境。而 ssh-add 用于向这个高速缓冲添加私钥。我重新用给 think-next 配置了新的 SSH 公钥，但我没有使用 ssh-add 进行操作，会不会是这个原因呢？

求助大模型，使用 `ssh-agent` 管理多个 GitHub 账号的 SSH 密钥是一种有效的方法。大模型给出的解题步骤：

1. **生成多个SSH密钥：**
    
    为每个GitHub账号生成单独的SSH密钥。例如：
    

```bash

   # 为账号一生成密钥
   ssh-keygen -t rsa -b 4096 -C "email1@example.com"
   # 保存文件名可以设为：~/.ssh/id_rsa_github_account1

   # 为账号二生成密钥
   ssh-keygen -t rsa -b 4096 -C "email2@example.com"
   # 保存文件名可以设为：~/.ssh/id_rsa_github_account2

```

1. **启动ssh-agent:**
    
    确保`ssh-agent`正在运行，可以通过以下命令启动它：
    

```bash

   eval "$(ssh-agent -s)"

```

1. **添加SSH密钥到ssh-agent:**
    
    使用`ssh-add`命令将生成的密钥添加到`ssh-agent`：
    

```bash

   ssh-add ~/.ssh/id_rsa_github_account1
   ssh-add ~/.ssh/id_rsa_github_account2

```

1. **配置`~/.ssh/config`：**
    
    修改SSH配置文件以区分不同的GitHub账号。在`~/.ssh/config`中添加以下内容：
    

```

   # 账号一
   Host github-account1
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_rsa_github_account1

   # 账号二
   Host github-account2
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_rsa_github_account2

```

1. **克隆和访问仓库：**
    
    当你克隆或访问某个仓库时，要指定对应的主机别名。例如：
    

```bash

   git clone git@github-account1:username/repository.git
   git clone git@github-account2:username/repository.git

```

通过这种方式，你可以方便地在同一台机器上管理多个GitHub账号的SSH密钥。务必确保你将每个账号的公钥添加到了相应GitHub账号的SSH keys设置中。