---
layout: post
category: posts
title: 搭建IPV6 VPN 实现教育网免费上网
tags: VPN,科学上网,SoftEtherVPN,IPV6
keywords: SoftEtherVPN,VPN,翻墙,免费上网
excerpt: 通过使用SoftEtherVPN在服务器上搭建VPN，实现IPV6代理流量，支持教育网免流量上网。使用国外的VPS进行搭建，实现科学上网。
redirect_from:
  - /2017/06/about/
---
很多程序员入门的第一步都是先翻墙....哈，我乱说的 
![02](/assets/images/2017-06/02.jpg)
第一件事当然是编写Hello World。但不可否认，会翻墙已经是程序员的必备技能了。今天就来教大家在服务器上搭建自己的VPN。这里使用的服务器是[CentOS](https://www.centos.org/ "CentOS")

搭建服务器的VPS使用的是[bandwagonhost](https://bandwagonhost.com/)，由于搬瓦工支持IPV6，在搭建好VPN之后使用教育网使用IPV6的地址连接VPN，发现竟然可以**免流量**连接网络。这么好的事，当然得造福广大祖（ku）国（bi）栋（xue）梁（sheng）。
![01](/assets/images/2017-06/01.jpg)

废话不多说，开始搞起......

## SoftEtherVPN介绍
[SoftEtherVPN](https://www.softether.org/ "SoftEtherVPN")是由日本筑波大学的登大遊在硕士论文中提出的开源、跨平台、多重协议的虚拟专用网方案，是专门为穿过防火墙而设计的。我们可以用它在自己的VPS上搭建一个简单的VPN来使用。
## 服务器端搭建
### 下载解压
首先要在服务器上下载并解压安装文件，注意服务器是32位还是64位，分别下载对应的文件。

32位系统

    wget http://www.softether-download.com/files/softether/v4.22-9634-beta-2016.11.27-tree/Linux/SoftEther_VPN_Server/32bit_-_Intel_x86/softether-vpnserver-v4.22-9634-beta-2016.11.27-linux-x86-32bit.tar.gz
    
64位系统

    wget http://www.softether-download.com/files/softether/v4.22-9634-beta-2016.11.27-tree/Linux/SoftEther_VPN_Server/64bit_-_Intel_x64_or_AMD64/softether-vpnserver-v4.22-9634-beta-2016.11.27-linux-x64-64bit.tar.gz

以上为截止发布本文时的最新版本，建议从[SoftEther]()官方网站获取最新版本。



### 安装启动

解压下载的文件
    
    tar -zxvf softether-vpnserver-*.tar.gz

将解压的文件移动到**/usr/local**下

    mv vpnserver /usr/local

进入解压目录

    cd /usr/local/vpnserver

启动安装脚本
    
    ./.install.sh


> SoftEther VPN Server (Ver 4.22, Build 9630, Intel x64 / AMD64) for Linux Install Utility
Copyright (c) SoftEther Project at University of Tsukuba, Japan. All Rights Reserved.

> Do you want to read the License Agreement for this software ?
> 
> 1.Yes
> 
> 2.No
> 
> Please choose one of above number:


这里询问你是否阅读License，输入1


> Did you read and understand the License Agreement ?
> 
> (If you couldn’t read above text, Please read ‘ReadMeFirst_License.txt’ file with any text editor.)
> 
> 1.Yes
> 
> 2.No
> 
> Please choose one of above number:


询问你是否读懂了，输入1


> Did you agree the License Agreement ?
> 
> 1.Agree
>
> 2.Do Not Agree
> 
> Please choose one of above number:


问你同不同意，继续输入1


接下来便会开始安装，假如遇到gcc命令无法识别，使用

    yum install gcc

命令安装，接下来重新执行安装命令。

如果没有异常则说明安装成功，执行

    ./vpnserver start

启动服务

    ./vpnserver stop

停止服务。




### 服务器端配置

启动成功后我们需要设置远程登录密码以便本地管理服务。
运行

    ./vpncmd

进入VPN的命令行：


> vpncmd command - SoftEther VPN Command Line Management Utility
> 
> SoftEther VPN Command Line Management Utility (vpncmd command)
>
> Version 4.22 Build 9634 (English)
>
> Compiled 2016/11/27 21:59:35 by yagi at pc30
Copyright (c) SoftEther VPN Project. All Rights Reserved.
> 
> By using vpncmd program, the following can be achieved.
> 
>
> 1.Management of VPN Server or VPN Bridge
>
> 2.Management of VPN Client
>
> 3.Use of VPN Tools (certificate creation and Network Traffic Speed Test Tool)
>
> Select 1, 2 or 3:


这里我们选择1，然后出现：


> Specify the host name or IP address of the computer that the destination VPN Server or VPN Bridge is operating on.
>
> By specifying according to the format ‘host name:port number’, you can also specify the port number.
(When the port number is unspecified, 443 is used.)
> 
> If nothing is input and the Enter key is pressed, the connection will be made to the port number 8888 of localhost (this computer).
>
> Hostname of IP Address of Destination:


这里需要选择地址和端口。由于这台VPS已经搭建了一个网站，用了SSL占用了443端口，所以默认的443端口是用不了了，所以一定要改。我改用了5555端口，所以在这里输入localhost:5555，然后出现：


> If connecting to the server by Virtual Hub Admin Mode, please input the Virtual Hub name.
If connecting by server admin mode, please press Enter without inputting anything.
Specify Virtual Hub Name:

这里就是指定一个虚拟HUB名字，用默认的直接回车就行
> Connection has been established with VPN Server “localhost” (port 5555).
>
> You have administrator privileges for the entire VPN Server.
>
> VPN Server>


这时我们需要输入

    ServerPasswordSet

命令设置远程管理密码


输入设定的密码，确认密码后就可以通过Windows版的SoftEther VPN Server Manager远程管理了。


## VPN管理


首先下载并安装[SoftEther VPN Server Manager](http://softether-download.com/en.aspx "SoftEther VPN Server Manager")，其实只用到了管理工具：

![vpnsetup](/assets/images/2017-06/vpnsetup.png)

安装之后运行它：

![vpnnew](/assets/images/2017-06/vpnnew.png)

在这里点**新建**：

![vpnset](/assets/images/2017-06/vpnset.png)

Host Name填服务器的地址或域名，端口如果之前改过了在这也记得改过来，右下角的密码填之前设置过的密码。新建完成后Connect就会弹出Easy Setup窗口（如果提示连不上请检查一下服务器的防火墙设置）：

![vpneasy](/assets/images/2017-06/vpneasy.png)

这里在第一个**远程连接**选择然后下一步即可，虚拟HUB名像之前一样默认就好。

然后会弹出一个动态DNS功能的窗口，由于不能确定它给的域名是不是被墙了，我们就不去用它，把这个窗口关了就行了。

之后会有一个协议设置的窗口：

![vpnl2tp](/assets/images/2017-06/vpnl2tp.png)


这里把启用**L2TP**挑上钩，下面设置一个**IPsec预共享密钥**就行了。

之后又会弹出一个**VPN Azure Cloud**服务的窗口，感觉没什么用，禁用了就行了。即使有用以后也可以再启用。

接下来要新建用户：

![vpnuser](/assets/images/2017-06/vpnuser.png)

其中用户名是必填的，验证类型选密码验证就行，然后在右侧设置用户密码

之后回到管理界面，点管理虚拟HUB：

![vpnmanager](/assets/images/2017-06/vpnmanager.png)

![vpnhub](/assets/images/2017-06/vpnhub.png)

这里也可以继续添加新用户。点**虚拟NAT和虚拟DHCP服务器**，弹出窗口：

![vpnvirtual](/assets/images/2017-06/vpnvirtual.png)

在里面启用**SecureNAT**并点**SecureNAT**配置：

![vpnnat](/assets/images/2017-06/vpnnat.png)

注意DNS要改为**8.8.8.8**和**8.8.4.4**。

这里就算配置完毕。然后顺便生成一下**OpenVPN**的配置文件，点**OpenVPN / MS-SSTP Setting**：

![vpnopen](/assets/images/2017-06/vpnopen.png)

然后点击新窗口中部的按钮生成配置文件：

![vpnovpn](/assets/images/2017-06/vpnovpn.png)

打开导出的压缩包会看到里面有两个**.ovpn**文件，一般我们要用到***_openvpn_remote_access_l3.ovpn**这个文件，因为我们是通过IPv4地址管理的VPN，所以这个配置文件里的**remote**一项会是IPv4地址，如果需要使用IPv6的VPN就将其替换为相应的IPv6地址即可。

## 连接VPN
### WINDOWS连接
首先到**网络和共享中心**里新建一个网络连接:

![intent](/assets/images/2017-06/intent.PNG)

选择连接到工作区 。

![vpn](/assets/images/2017-06/vpn.PNG)

连接VPN

![host](/assets/images/2017-06/host.PNG)

然后在地址栏里填上服务器地址或域名（**这里填写对应VPS的IPV6地址**）。配置完成后打开**更改适配器设置**：

![info](/assets/images/2017-06/info.PNG)

打开VPN的属性

![security](/assets/images/2017-06/security.PNG)

在**安全**选项卡里将**VPN类型**改为**L2TP**，然后点高级设置：

![key](/assets/images/2017-06/key.PNG)

在里面选上使用预共享密钥并把之前设置的密钥填进去。

![vpnconnect](/assets/images/2017-06/vpnconnect.PNG)

之后在VPN连接里填入服务器地址，用户名和密码填进去就可以连上了。

Windows10自带的VPN支持IPV6，只要按照上面的方面将域名改为IPV6的地址就可以使用IPV6流量，实测教育网可以通过该方法免流量上网。

接下来，享受美好的网络世界吧.....