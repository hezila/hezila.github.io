---
layout: post
title: SSH远程访问服务器（使用动态IP）
tags: [ssh, remote-machine]
keywords: [ddns, ssh, 动态IP，动态DNS]
---

最为程序员，一般我们远程访问机器的时候都是通过SSH来连接访问的。首先你要在需要远程访问的机器上安装 `openssh-server`。使用一下命令安装：

    sudo apt-get install openssh-server

安装启动服务后，你就可以通过ssh命令远程访问了(使用自己的用户名和IP地址)

    ssh USERNAME@IPADDRESS

这里最大的问题就是机器是通过IP来访问的。如果我们的机器使用的是动态IP的时候，我们就需要每次连接前确认机器的IP地址。


### 解决方案1 - 使用动态DNS (DDNS) 服务

来源：[http://askubuntu.com/questions/72349/how-to-run-an-ssh-server-without-a-static-ip-address](http://askubuntu.com/questions/72349/how-to-run-an-ssh-server-without-a-static-ip-address)

> **什么是 DDNS ？**
>
> DDNS （ Dynamic Domain Name Server ）是动态域名服务的缩写。 DDNS 是将用户的动态IP 地址映射到一个固定的域名上，用户每次连接网络的时候客户端程序就会通过信息传递把该主机的动态IP 地址传送给位于服务商主机上的服务器程序，服务项目器程序负责提供 DNS 服务并实现动态域名解析。 DDNS 的主要作用就是捕获用户每次变化的 IP 地址，然后将其与域名相对应，这样其他上网用户就可以通过域名来与用户交流了。


使用提供DDNS服务的平台，例如`dyndns.com`, 国内的 `dnspod.cn` 和 `cloudxns.net` 都提供这样的服务。以 `dyndns.com` 为例，

1. 首先我们在 `dyndns.com` 上注册账户，系统会为我们分配一个URL (请求这个URL，可以返回一个IP地址)。

2. 在我们的机器上下载 `ddclient`, 这个工具可以自动更新IP地址到`dyndns.com`，这样，每次我们访问`dyndns.com`分配给我们的URL，就可以获得机器最新的IP地址了。

3. 接下来，你就可以通过URL来SSH访问这台机器了。

> 注意：如果有交换机和路由，一定要确保router开放了 `22` 端口。

### 解决方案2 - 使用永久ssh通道 (persistent reverse ssh tunnel)

来源：[http://www.vdomck.org/2005/11/reversing-ssh-connection.html](http://www.vdomck.org/2005/11/reversing-ssh-connection.html)

假设你有两台机器，一台公司分配的工作站（在公司里）和一台笔记本laptop（在家中）。可能由于公司防火墙的原因，你不能连接到你的工作站。现在可以让你的公司同事Tom（他现在在公司内，可以访问到公司服务器）登录你的工作站，然后使用下面的命令ssh连接你的laptop：

    ssh -f -N -R 10000:localhost:22 username@ip_address_of_laptop

这个命令可以建立一个从工作站到你笔记本的ssh连接。参数 `-R 10000:localhost:22` 告诉laptop监听端口10000，并且转发这个端口接收的任意请求到工作站。所以现在，你可以通过端口10000和你的laptop本机建立一个ssh连接 (实际上就是ssh到你的工作站)：

    ssh username@localhost -p 10000

作为一个程序员，以上的方法还需要同事帮忙，是非常羞耻的。所以，你可以在你的工作站创建一个cron任务尝试构建ssh连接到你的laptop（每小时）。如果你的laptop的ip是变化的，请参考第一种解决方法，动态获取laptop的ip。


还有另外一种更好的方法，就是找一台中间机器来代理。这台服务器一直和你的工作站保持ssh连接。

1. 首先，编辑中间代理机器的 `/etc/sshd_config`， 使得建立的ssh连接是长期有效的，不回自动断开,

```
TCPKeepAlive yes
ClientAliveInterval 30
ClientAliveCountMax 99999
GatewayPorts yes
```

2. 然后重启ssh的，确保以上的修改生效

```
ps -aux | grep sshd
kill -hup
```
3. 在工作站上建立ssh连接到中间代理机器

    nohup ssh -f -N -R 1000:localhost:22 username@middle_pc

4. 然后，从你的laptop上通过10000端口连接到中间代理机器，它会转发你的请求到工作站

    ssh username@middle_pc -p 10000

这种方法应该是最经济的方法了。

**更多参考：**

- [https://juliansimioni.com/blog/howto-access-a-linux-machine-behind-a-home-router-with-ssh-tunnels/](https://juliansimioni.com/blog/howto-access-a-linux-machine-behind-a-home-router-with-ssh-tunnels/)
