---
title: lftp
date: 2017-02-12 21:27:32
tags: lftp
---
`lftp`是一个很强大的工具，一点小皮毛的工具就解决了我的很多问题。`lftp` 相当给力地支持`sftp`，而且支持`tab`自动补全，稍加配置就可以使自己的工具很高效。

### 1. lftp bookmark
在你使用lftp登录sftp后，可以使用bookmark记住此会话，具体使用办法参考
```bash
help bookmark
```
这些bookmark会保存在`~/.local/share/lftp/bookmarks`文件里 往`~/.bash_profile`里添加下述代码，可以实现lftp tab补全bookmark
```bash
complete -W "$(echo `cat ~/.local/share/lftp/bookmarks |cut -f 1`;)" lftp
```

### 2. lftp mirror
这是一个相当给力的功能，可以把服务器上的一个文件夹和本地的一个文件夹进行映射，使用方法照旧
```bash
help mirror
```
本人常用的是
```bash
mirror -R xxx xxx 
```
同时mirror 命令支持正则来排除一些无需同步的文件夹及文件。 e.g:
```bash
mirror --exclude logs/ --exclude reports/ --exclude-glob *.bak
```

### 3. lftp 总结
当然lftp 功能不止这些，这只是冰山一脚，我只把我常用的这些功能列出来。它的mirror功能可以提供同步备份，具体使用办法，网上比较多，我也就不多说了，此处重点是mirror 里的正则，网上资料少，希望给读者一些帮助吧。