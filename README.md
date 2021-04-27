# VPN_Tutorial
A tutorial includes how to set up YOUR OWN VPN/VPS via Amazon EC2 instances  
一篇包含如何设置自己的VPN/VPS的教程，可以用来科学上网，12个月免费使用，基本完全免费。  
核心思想是设置/拥有一台能够无限制访问外网的机器（云主机/云服务器），这台机器作为中间节点，接受你本地机的流量，然后转发给目标网站/目标服务器。  
目标服务器收到请求后，将请求内容返回给中间节点，然后中间节点再转发给本地机.  
最好有一点点linux基础知识，比如`cd`,`ls`命令  

## Apply for a instance in Amazon EC2.
1.创建一个AWS账号，跟着提示走，注意付款方式只有信用卡，只支持Visa或Mastercard等国外信用卡，不支持国内的银联Unionpay。
2.亚马逊为了验证信用卡有效性会扣1USD，过几天会还回去。  
3.验证完就可以免费试用 T3.Micro实例 12个月，每月可用时长750小时（正好够1台实例一直运行）。  

实测上下行速度，跑满家用百兆宽带十分轻松。  
![image](https://user-images.githubusercontent.com/48174333/116256439-c581e980-a7a5-11eb-909a-30112ba3668f.png)

4.之后就可以访问Amazon EC2的控制台。  
5.注意，AWS默认区域是w美国，不同区域之间数据不互通。在国内为了速度（同时可看B站港澳台），建议切换到香港地区。  
6.点击右上角启动新实例。  
7.选择Linux系统，因为超出12个月还想用的话，资费会比Windows的便宜很多（Windows需要额外授权费）。这里我选择`Ubuntu Server 20.04 LTS (HVM), SSD Volume Type - ami-0774445f9e6290ccd (64 位 (x86)`。  
8.
9.
https://ap-east-1.console.aws.amazon.com/ec2/v2/home?region=ap-east-1#Instances:
