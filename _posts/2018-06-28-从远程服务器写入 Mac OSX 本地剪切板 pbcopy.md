---
layout: post
title: 从远程写入 OSX 本地剪切板 pbcopy 
---

### **1.** 端口转发
将远程服务器的某个端口（比如10022）转发到本地的 22 端口，以便远程服务器可以ssh到本地。
```sh
ssh -R 10022:localhost:22 remoteServer
```

如果嫌每次敲这个命令比较麻烦，那么就在`~/.ssh/config`添加下面几行，之后每次ssh，都会自动打开端口转发。
```sh
Host *
  RemoteForward 10022 localhost:22
```

### __2.__ 将服务器的 `~/.ssh/id_rsa_pub` 添加到本地
```sh
ssh-copy-id username@localhost -p 10022
```

### __3.__ 在远程主机写剪切板
```sh
echo "xixixi" | ssh -p 10022 localhost pbcopy
```

### __4.__ 在本地查看
在本地打开终端，输入pbpaste，可以看到显示 `xixixi`。  
也可以在任意文本框，右键粘贴，可以看到显示 `xixixi`。


### __5.__ 有什么用
搭配 `Tmux` 或者是 `Vim` 等工具使用。