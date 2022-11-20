# 情景描述

假设现在有三台机器，分别是LocalMachine，Mac，以及DevMachine。  
其中LocalMachine可以单向访问Mac，Mac可以单向访问DevMachine，目标是让LocalMachine可以单向访问DevMachine。  
Input：LocalMachine -> Mac -> DevMachine.  
Ouput: LocalMachine -> DevMachine.  

# 配置ssh config文件

在LocalMachine上作如下配置：

C:\Users\tengx> cat C:\Users\tengx\.ssh\config
~~~
# Specify our intermediate jump host, nothing fancy here
# we just tell what the host name is for now.
Host Mac
    HostName $ip_of_Mac
    User $UserName1

# Now we will specify the actual remote host with
# the jump host as the proxy. Specify remote hostname
# as the jump-host would see it since we will be connecting
# from the jump host.
Host DevMachine
    HostName $ip_of_DevMachine
    ProxyCommand ssh -W %h:%p Mac
    User $UserName2
~~~

# 访问无法直接连通的目标机器

将上面配置中的$ip_of_Mac替换为Mac在网段中的ip，通常是挂在同一个家用路由器下的，如“192.168.0.1”之类的。  
将上面配置中的$ip_of_DevMachine替换为DevMachine的内网IP，该IP在LocalMachine机器上无法访问，例如说无法ping通。  
但是由于设置了ProxyCommand，所以LocalMachine会在Mac的ssh连接中进行二次跳转访问DevMachine。  
最后只需要在LocalMachine上直接执行ssh命令即可：  
~~~
ssh DevMachine
~~~