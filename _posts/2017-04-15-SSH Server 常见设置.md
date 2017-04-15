---
layout: psot
title: SSH Server常用配置
---

## 生成密钥
`ssh-keygen -A`
## 修改/etc/ssh/sshd
```
ListenAddress 0.0.0.0
PasswordAuthentication yes
PermitRootLogin yes
   
```