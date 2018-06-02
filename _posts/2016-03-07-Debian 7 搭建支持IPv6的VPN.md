---
layout: post
title: Debian7搭建支持IPv6的OpenVPN
---

学校宿舍可以使用**ipv6**，ipv6不限速，而ipv4限速2Mbps，遂用一个ipv6的代理来访问ipv4的网络资源。平常使用的**shadowsocks**并不适合这种全局代理的场景，因为需要修改分别去每个应用程序的代理设置，遂尝试搭建一个**VPN**。



#### 在Debian上安装OpenVPN

 `#apt-get install openvpn`

默认情况下，会自动安装easy-rsa脚本，将其移动到需要的位置，可以是/etc/openvpn/easy-rsa

找到easy-rsa位置并创建目录

`#find / -name easy-rsa` 

`#mkdir /etc/openvpn/easy-rsa  `

复制脚本

`# cp -r /usr/share/doc/openvpn/examples/easy-rsa/2.0/* /etc/openvpn/easy-rsa/`



#### 生成CA证书和CA密钥

修改vars文件，设置证书的组织信息

``` shell
#cd /etc/openvpn/easy-rsa
#vi ./vars
```



根据实际情况修改下面项目（也可不修改），记住KEY_NAME

``` shell
export KEY_COUNTRY="CH"
export KEY_PROVINCE="NM"
export KEY_CITY="HOHHOT"
export KEY_ORG="baicai"
export KEY_EMAIL="radon86@live.cn"
export KEY_NAME=vpnbaicai
export KEY_OU=baicai
```

输出修改的信息

`#source ./vars`

清理以前生成的所有证书

`#./clean-all`

生成CA证书和密钥

`#./build-ca`

生成服务其证书,使用到上面的KEY_NAME的值

`#./build-key-server vpnbaicai`

生成Diffie Hellman PEM证书

`#./build-dh`

生成客户端证书，'baicai'可以是任意一个字符串

`#./build-key baicai`

生成HMAC

` #openvpn --genkey --secret /etc/openvpn/easy-rsa/keys/ta.key`

复制证书到client和server

* ca.crt在client和server上
* ca.key在client上
* server还需要server.crt, dh1024.pem, server.key和ta.key
* client还需要client.crt, client.key 和ta.key

设置服务器上的证书和密钥

```shell
#mkdir /etc/openvpn/certs
#cp -pv /etc/openvpn/easy-rsa/keys/{ca.{crt,key},vpnbaicai.{crt,key},ta.key,dh1024.pem} /etc/openvpn/certs/
```



#### 配置OpenVPN服务器

打开(新建)配置文件

`#vi /etc/openvpn/server.conf`

修改如下

```sh
port 1194
proto udp
proto udp6
dev tun
server 192.168.234.0 255.255.255.0
server-ipv6 2001:db8:0:123::/64
ifconfig-pool-persist ipp.txt
push "redirect-gateway def1" 
push "dhcp-optopenvpn --genkey --secret /etc/openvpn/easy-rsa/keys/ta.keyion DNS 8.8.8.8"
push "dhcp-option DNS 8.8.4.4"
ca /etc/openvpn/certs/ca.crt
cert /etc/openvpn/easy-rsa/keys/baicai_vpn.crt
key /etc/openvpn/easy-rsa/keys/baicai_vpn.key
dh /etc/openvpn/easy-rsa/keys/dh1024.pem
keepalive 10 120
cipher AES-256-CBC 
comp-lzo
persist-key
persist-tun
status /var/log/openvpn-status.log #日志文件
verb 3
```

打开服务器IP转发

`echo 1 >/proc/sys/net/ipv4/ip_forward`

设置OpenVPN开机启动

`#update-rc.d -f openvpn defaults`

重启OpenVPN服务

`#service openvpn restart`

#### 配置网络转发

将下面内容加入/etc/rc.local的末尾

```sh
echo 1 > /proc/sys/net/ipv4/ip_forward
```

设置iptables规则

```sh
iptables -A FORWARD -m state --state RELATED,ESTABLISHED -j ACCEPT
iptables -A FORWARD -s 192.168.234.0/24 -j ACCEPT
iptables -A FORWARD -j REJECT
iptables -t nat -A POSTROUTING -s 192.168.234.0/24 -o eth0 -j MASQUERADE
iptables -t nat -A POSTROUTING -j SNAT --to-source 45.32.42.34
```





# **注意**

* 如果你是在windows下编辑配置文件再上传到Linux服务器，可能会存在一些不被识别的字符，从而导致OpenVPN启动失败




