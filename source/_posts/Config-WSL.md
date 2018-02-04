---
title: 配置 Windows SubSystem Linux
date: 2018-01-28 14:56:42
tags: Windows SubSystem Linux
---

# 1. `Windows SubSystem Linux` 介绍

个人Linux属于一帮爱折腾家伙的玩物，之前想玩一般是在虚拟机里玩，而`Windows` 在 `Windows 10` 里直接引入了这么一个子系统，可以说这还是相当给力的。

有很多开发者都喜欢用 `Mac`，包括我，他们给出的理由是 `OSX` 对开发者友好，而 `WSL` 的引入恰好可以弥补这一点，为 `Windows` 系统争取更多的使用者。

就我本人而言，还是喜欢看到这种竞争的。

安装就不说了，下面直接上配置，本人装的是 `Ubuntu 16.04`，另此文会不断更新。

# 2. 配置 `zsh`

## 2.1 安装 `zsh`

``` bash
sudo apt install zsh
chsh -s /bin/zsh
```

## 2.2 安装 `oh-my-zsh`

``` bash
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```
# 3. 配置 `SSH`

## 3.1 安装 `SSH`

``` bash
sudo apt install openssh
sudo service ssh start
```

## 3.2 配置

做完上述的应该是连不上的，需要修改下配置

```bash
sudo vim /etc/ssh/sshd_config
```

内容如下：

```
 ListenAddress 0.0.0.0  #去掉原配置的注释即可；监听本地回环
 UsePrivilegeSeparation no # yes->no；不知道为什么，不改连接会理解关闭
 PasswordAuthentication yes # no->yes；不改只能使用秘钥登陆
 Port XXXX # 默认的22可能需要特殊权限（Windows系统上），没有做过测试。
```

# 4. 配置 `Spacemacs`

## 4.1 安装 `emacs25`

``` bash
sudo apt update
sudo apt upgrade
sudo add-apt-repository ppa:kelleyk/emacs
sudo apt update
sudo apt install emacs25
```

## 4.2 安装 `spacemacs`

``` bash
git clone https://github.com/syl20bnr/spacemacs ~/.emacs.d
```

安装之后在 `xshell` 里显示效果会有问题，在 `~/.zshrc` 里添加

``` bash
export TERM=xterm-256color
```

`xshell` 里把界面字体改成 `Source Code Pro for Powerline`，具体配置参考下图

![](/images/wsl_xshell_conf.PNG)

# 5. 配置 `git`

``` bash
git config --global core.editor "vim"
```