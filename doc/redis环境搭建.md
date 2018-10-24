### redis单机版部署
[redis官方下载安装说明](https://redis.io/download)
```bash
# 1. 下载redis安装包
wget http://download.redis.io/releases/redis-5.0.0.tar.gz

# 2. 解压编译
tar zxvf redis-5.0.0.tar.gz
cd redis-5.0.0
make

# 3. 配置redis后台运行
vim redis.conf
daemonize yes

# 3. 编译生成的二进制文件在./src目录,运行启动
src/redis-server ./redis.conf

# 4. 使用内置的redis客户端与redis交互
src/redis-cli

# 5. 拷贝脚本到系统bin目录
sudo cp src/redis-server redis-cli /usr/local/bin/

```
---
### redis集群版部署
  既可以在一台linux上配置的redis集群，也可以部署在多台linux服务器上.
#### 创建相关目录
```bash
# 1. 创建redis-cluster文件夹,然后在其下面分别创建6个文件夹如下(三个主节点,三个从节点)
# 为什么至少3主节点?Redis Cluster是一个实现了分布式且允许单点故障的Redis高级版本，它没有中心节点，具有线性可伸缩的功能。至少三个节点，而且搭建多节点时，必须使用奇数个节点，否则会出现脑裂的情况，导致集群崩溃。
mkdir -p $HOME/software/redis-cluster/{7001,7002,7003,7004,7005,7006}

# 2. 把redis.conf文件分别copy到刚刚新建的６个文件夹下，进行修改各个配置文件内容，也就是700*下的每一个redis.conf文件进行修改
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7001/
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7002/
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7003/
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7004/
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7005/
cp redis/redis-5.0.0/redis.conf ./redis-cluster/7006/
```
#### 修改每个redis实例配置文件
  当然你可以先修改7001下面的redis.conf之后,然后吧这个文件依次丢到7002....7006里面,然后只需使用vim的替换命令:%s 7001/700x即可
```bash
# redis后台运行
daemonize yes
# redis实例运行端口
port 7001
# 指定只接收来自该ip地址的请求
bind 127.0.0.1
# 指定数据文件存放位置,必须要指定不同的目录位置,不然会丢失数据
dir ./
# 启动集群模式
cluster-enabled yes
# 集群配置文件,在集群启动时,自动创建.这里最好和700*和端口号对应上
cluster-config-file node-7001.conf
# 集群超时时间,节点超时多久认为它宕机了
cluster-node-timeout 5000
# 配置持久化方式:开启aof持久化模式,每次写操作请求都追加到appendonly.aof文件中
appendonly yes
# 每次有写操作的时候都同步
appendfsync always

# 检查配置:过滤注释行和空白航
cat redis.conf  | grep -v  ^#  |grep -v ^$
```
#### 启动redis server
```bash
# 启动6个redis server实例
redis-server ./7001/redis.conf
redis-server ./7002/redis.conf
redis-server ./7003/redis.conf
redis-server ./7004/redis.conf
redis-server ./7005/redis.conf
redis-server ./7006/redis.conf

# 检查是否启动成功
ps -ef | grep redis

```
#### 创建集群
```bash
# redis5.0使用redis-cli作为创建集群的命令，使用c语言实现，不再使用ruby语言。
redis-cli --cluster create 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 --cluster-replicas 1
```
#### 检查集群
```bash
# 连接任何一个redis实例进行验证
redis-cli -c -h 127.0.0.1 -p 7001
127.0.0.1:7001> cluster info
127.0.0.1:7001> cluster nodes
```
#### 集群运维
##### 关闭集群
```bash
# 使用命令逐个关闭redis实例
redis-cli -c -h 127.0.0.1 -p 7001 shutdown
redis-cli -c -h 127.0.0.1 -p 7002 shutdown
redis-cli -c -h 127.0.0.1 -p 7003 shutdown
redis-cli -c -h 127.0.0.1 -p 7004 shutdown
redis-cli -c -h 127.0.0.1 -p 7005 shutdown
redis-cli -c -h 127.0.0.1 -p 7006 shutdown

#友情提示：
#当出现集群无法启动时，删除集群配置文件，再次重新启动每一个redis服务，然后重新构件集群环境。
```
##### 像集群中添加节点
```bash
...
```
##### 向集群中删除节点
```bash
...
```
---

### redis Desktop Manager
```bash
deepin系统直接搜索即可安装
```