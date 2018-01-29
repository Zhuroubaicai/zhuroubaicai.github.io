---
layout: psot
title: SSH Server配置
---

## 生成密钥
`ssh-keygen -A`
## 修改/etc/ssh/sshd_config
```
ListenAddress 0.0.0.0
PasswordAuthentication yes
PermitRootLogin yes
   
```