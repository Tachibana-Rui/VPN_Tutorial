# VPN_Tutorial
A tutorial includes how to set up YOUR OWN VPN/VPS via Amazon EC2 instances  
一篇非常详细，包含如何设置自己的VPN/VPS的教程，可以用来科学上网，12个月免费使用，基本完全免费。  
核心思想是设置/拥有一台能够无限制访问外网的机器（云主机/云服务器），这台机器作为中间节点，接受你本地机的流量，然后转发给目标网站/目标服务器。  
目标服务器收到请求后，将请求内容返回给中间节点，然后中间节点再转发给本地机   
最好有一点点linux基础知识，比如`cd`,`ls`命令，和一点计算机网络方面的知识。    
下文中，实例=服务器=VPS=虚拟机。作为维护用的本地机可以是任何具有终端/命令行功能的机器，只要它预装有SSH服务。Windows用户在系统功能里添加SSH组件即可,Mac/Linux自带，Android需要安装Termux。

## 在Amazon EC2里创建虚拟机.
1. 创建一个AWS账号，跟着提示走，注意付款方式只有信用卡，只支持Visa或Mastercard等国外信用卡，不支持国内的银联Unionpay。
2. 亚马逊为了验证信用卡有效性会扣1USD，过几天会还回去。  
3. 验证完就可以免费试用 T3.Micro实例 12个月，每月可用时长750小时（正好够1台实例一直运行）。  
4. 之后就可以访问Amazon EC2的控制台。  
5. 注意，AWS（Amazon Web Service）默认区域是美国，不同区域之间数据不互通。在国内为了速度（同时可看B站港澳台），建议切换到香港地区。  
6. 点击右上角启动新实例。  
7. 选择Linux系统，因为超出12个月还想用的话，资费会比Windows的便宜很多（Windows需要额外授权费）。这里我选择`Ubuntu Server 20.04 LTS (HVM), SSD Volume Type - ami-0774445f9e6290ccd (64 位 (x86)`。  
8. 实例类型选择`t3.micro`。  
9. 配置实例，把 积分规范：无限 前的勾去掉，其他默认。  
10. 添加密钥对，记得下载密钥文件（只有一次机会），并放到一个安全的位置。Windows系统需要修改密钥文件属性-安全-高级，其他用户权限删了，只留下当前用户，Linux直接`chmod 700 key.pem`，key.pem替换成你的密钥文件路径。  
11. 添加存储，改到30G（反正免费，不用白不用）。  
12. 添加标签，可加可不加，加了便于管理。  
13. 配置安全组，这个很关键，在下面的部分详细说明。可以先跳过，启动实例，之后再修改安全组规则，也可以直接在这改。  
14. 审核并启动实例。 
15. 这时候回到控制台主页可以看到，你已经创建了第一个实例。之后可以通过任何终端/命令行窗口，根据创建完成实例后的提示信息，通过SSH访问该实例。  

当然你也可以在其他区域创建实例，根据你的所在地，选择一个较近的位置，能降低从本机到VPS的延迟。  
香港区控制台地址`https://ap-east-1.console.aws.amazon.com/ec2/v2/home?region=ap-east-1#Instances:`  

## 安全组设置
安全组本质是防火墙规则。简单来说，就是允许 来自哪的IP，访问实例的哪个端口 的数据包通过。
控制台左侧，点击网络与安全-安全组，点击launch-wizard-1对应的安全组ID，编辑入站规则，如下图所示。  

![image](https://user-images.githubusercontent.com/48174333/116269174-8e650580-a7b0-11eb-887e-59187a949fa8.png)

当然，你也可以只选择开放你需要的端口，除了SSH的22端口都不是必须的（当然得额外开放一个用于科学上网的端口）。我是为了图省事开放了8000-18000的端口，便于之后除了科学上网，还可以用于各种Linux项目练习。  

## 分配弹性IP
弹性IP就是公网IP，你需要从AWS香港的IP池里选一个，与自己的实例绑定，便于外部访问。  
因为默认提示的SSH访问方式给的是一个公共域名，根据你的Key文件登录对应的实例，这没法用在ShadowsocksR里，所以需要一个公网IP作为你的服务器地址。  
控制台左侧，点击网络与安全-弹性IP，右上角分配弹性IP地址，从AWS香港的IP池里抽一个，点击分配，这样你就得到了一个公网IP。  

![image](https://user-images.githubusercontent.com/48174333/116273319-3c25e380-a7b4-11eb-9e31-6f20a356815e.png)

然后，你需要绑定IP到你的实例，勾选你的IP，右上角操作-关联弹性 IP 地址，资源类型选择 实例，从实例的下拉列表里关联上你的实例。  

![image](https://user-images.githubusercontent.com/48174333/116273080-07b22780-a7b4-11eb-9ca1-7ac0852c648c.png)

之后，就可以通过命令提示符输入  
`ssh -i D:\Documents\key.pem ubuntu@18.x.y.z`  
D:\Documents\key.pem 换成你的密钥文件的绝对路径，18.x.y.z替换成你的实例公网IP。  
这样你就成功进入了实例的操作界面。  

![image](https://user-images.githubusercontent.com/48174333/116275794-80b27e80-a7b6-11eb-9fd1-adc853c2ca08.png)

## 部署Shadowsocks服务端
在云主机中执行命令  
`sudo su
wget --no-check-certificate https://raw.githubusercontent.com/ShadowsocksR-Live/shadowsocksr-native/master/install/ssrn-install.sh
chmod +x ssrn-install.sh
./ssrn-install.sh 2>&1 | tee ssr-n.log`

安装完成后，会进行初次使用的服务器配置。
1. Server Port 服务器端口 可以任意指定，但是不要与系统服务和常用软件的端口冲突，推荐10000以后；  
2. Password 密码 可以任意指定，自己记住；  
3. Encryption Method 加密方法 可以任意指定，推荐安全性高的AEAD类方法，比如chacha20-ietf；  
4. Protocol 协议 选 origin；  
5. obfs 混淆器 选 plain；  

接下来在本地机配置ShadowsocksR客户端。总体来说大同小异，客户端设置里，服务器IP填写实例IP，远程端口/加密方法与自己设的保持一致。之后就可以尽情享受了。




实测上下行速度，跑满家用百兆宽带十分轻松。  
![image](https://user-images.githubusercontent.com/48174333/116256439-c581e980-a7a5-11eb-909a-30112ba3668f.png)

