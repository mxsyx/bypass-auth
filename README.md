# 绕过校园网web认证

### 教程
绕过校园网WEB认证_dns2tcp实现 https://www.cnblogs.com/nkqlhqc/p/7805837.html

绕过校园网WEB认证_iodine实现 https://www.cnblogs.com/nkqlhqc/p/7880326.html


### dns2tcp 配置

服务端: <安装dns2tcpd>
	
配置文件(/etc/dns2tcpd.conf)
listen = 10.0.0.4（Linux服务器的内网IP）    
port = 53    
user = nobody    
chroot = /tmp    
domain = dnsnkqlhqc.top（NS记录值）    
resources = ssh:127.0.0.1:22,socks:127.0.0.1:1082,http:127.0.0.1:3128

后台运行：
>>创建screen会话         screen -S dns2tcpd  
>>执行我们想要的命令     sudo dns2tcpd -f /etc/dns2tcpd.conf -F -d 2  
>>暂时离开快捷键         Ctrl + a d
>>恢复screen会话         screen -r dns2tcpd  
>>列出当前的会话列表     screen -ls  
>>强行终止               screen -S dns2tcpd -X quit 

客户端:<安装dns2tcpc>

dns2tcpc -r ssh -z dnsnkqlhqc.top 139.219.106.116 -l 8888 -d 2  

-r 后接服务名称<ssh/socks/http>
-z 后接NS记录, VPS公网ip
-l 后接本地端口
-d 开启 Debug

Xshell连接服务器转换Socks4/5通用代理：***Windows需要，Linux无需

新建会话：  
IP<127.0.0.1>  
端口<8888>
隧道：类型<socks4/5>  端口<1080>

Windows：Internet选项->socks代理->IP<127.0.0.1>  端口<1080>
Linux：  网络设置->代理->手动->socks代理->IP<127.0.0.1>  端口<8888>

***注:服务器添加入站与出站规则


### iodine 配置

服务端：<安装iodined与openVPN>

>后台运行：
>创建screen会话         screen -S iodined  
>执行我们想要的命令     iodined -c -f -P 123456 172.16.0.1 dnsnkqlhqc.top
>暂时离开快捷键         Ctrl + a d
>恢复screen会话         screen -r iodined  
>列出当前的会话列表     screen -ls  
>强行终止               screen -S iodined -X quit 

ping通172.16.0.1后继续

客户端：<安装iodine与openVPN>   ***仅Linux可用

iodine -f -P 123456 139.219.106.116 dnsnkqlhqc.top

ssh -D 127.0.0.1:8888 ntx@172.16.0.1

网络设置->代理->手动->socks代理->IP<127.0.0.1>  端口<8888>
