## 简介
`docker swarm`集群学习的部分资料

## 资料
1. 安装`docker-machine`  
命令：  
`$ base=https://github.com/docker/machine/releases/download/v0.16.0 &&  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine`  
`docker-machine version`验证版本  
![运行结果1]（https://github.com/Silence1017/docker-swarm/blob/master/%E5%9B%BE%E7%89%871.png）  

2. 安装`virtual box`  
官网下载压缩包  
`sudo dpkg -i xxx.deb`（如果报错运行`sudo -y -f install`修复一下）  
直接安装`virtualbox`扩展包  
此时新建虚拟机会报错，按照提示运行`sudo /sbin/vboxconfig`,`sudo apt install gcc`,`sudo apt install make`，之后运行`sudo /sbin/vboxconfig`成功。  

3. 创建swarm集群  
网址：http://objcoding.com/2018/08/12/docker-swarm/  
创建节点：`docker-machine create -d virtualbox worker1`  
进入`docker`虚拟主机：`docker-machine ssh worker1`  
将`worker1`主机设定为`swarm`管理节点：`docker swarn init`  
如果虚拟主机有两个以上网卡，它会提示报错：  
`Error response from daemon: could not choose an IP address to advertise since this system has multiple addresses on different interfaces (10.0.2.15 on eth0 and 192.168.99.100 on eth1) - specify one with --advertise-addr`  
所以需要选择一个与主机互通的网卡ip：  
`docker swarm init --advertise-addr 192.168.99.100`  
此时`worker1`已经成为了`swarm`集群管理节点。  
在管理节点界面运行`docker node ls`可查看该`swarm`集群所有节点  
![运行结果2]（https://github.com/Silence1017/docker-swarm/blob/master/%E5%9B%BE%E7%89%872.png）  
接着继续创建节点并将他们作为工作节点加入`swarm`集群：  
`$ docker-machine create -d virtualbox worker2`  
`$ docker-machine create -d virtualbox worker3`  
`$ docker-machine create -d virtualbox worker4`  
`$ docker-machine create -d virtualbox worker5`  
`$ docker-machine ssh worker2`  
`$ docker swarm join --token SWMTKN-1-69luztakii9ix7f5osezl0v6l2ibfzp1vqc0gbhcous63hm1fx-8p3vxanj97f2e0jflznihvl8f 192.168.99.100:2377`  
其余几个节点按照上面两个命令加入集群  
此时`swarm`集群就创建完成了。  
重开终端连入管理节点：`docker-machine start worker1`  
![运行结果3]（https://github.com/Silence1017/docker-swarm/blob/master/%E5%9B%BE%E7%89%873.png）  
系统会提示执行命令：`docker-machine env worker1`  
![运行结果4]（https://github.com/Silence1017/docker-swarm/blob/master/%E5%9B%BE%E7%89%874.png）  
再执行`eval $(docker-machine env worker1)`，则`worker1`节点成功开启。  
