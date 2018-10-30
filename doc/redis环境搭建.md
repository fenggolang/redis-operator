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
[redis-cluster官方教程](https://redis.io/topics/cluster-tutorial)

  既可以在一台linux上配置的redis集群，也可以部署在多台linux服务器上.
  
  **下面们首先部署一个三主(3个master)三从(3个slave)的redis集群**
  
#### 创建相关目录
```bash
# 1. 创建redis-cluster文件夹,然后在其下面分别创建6个文件夹如下(三个主节点,三个从节点)
# 为什么至少3主节点?Redis Cluster是一个实现了分布式且允许单点故障的Redis高级版本，它没有中心节点，
# 具有线性可伸缩的功能。至少三个节点，而且搭建多节点时，必须使用奇数个节点，否则会出现脑裂的情况，导致集群崩溃。
# 此时启动之后谁是那3个主节点谁是那3个从节点是随机的.
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
  当然你可以先修改7001下面的redis.conf之后,然后吧这个文件依次丢到7002....7006里面,然后只需使用
  vim的替换命令:%s 7001/700x即可
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
# 需要注意的是下面命令只需要执行一次就可以了,如果redis集群关闭,那么第二次直接启动每一个redis实例即可不用再执行下面组成集群的命令
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
##### 集群命令帮助文档
```bash
wangpengcheng@wangpengcheng-PC:~/software/redis-cluster$ redis-cli --cluster help
Cluster Manager Commands:
  create         host1:port1 ... hostN:portN
                 --cluster-replicas <arg>
  check          host:port
  info           host:port
  fix            host:port
  reshard        host:port
                 --cluster-from <arg>
                 --cluster-to <arg>
                 --cluster-slots <arg>
                 --cluster-yes
                 --cluster-timeout <arg>
                 --cluster-pipeline <arg>
  rebalance      host:port
                 --cluster-weight <node1=w1...nodeN=wN>
                 --cluster-use-empty-masters
                 --cluster-timeout <arg>
                 --cluster-simulate
                 --cluster-pipeline <arg>
                 --cluster-threshold <arg>
  add-node       new_host:new_port existing_host:existing_port
                 --cluster-slave
                 --cluster-master-id <arg>
  del-node       host:port node_id
  call           host:port command arg arg .. arg
  set-timeout    host:port milliseconds
  import         host:port
                 --cluster-from <arg>
                 --cluster-copy
                 --cluster-replace
  help           

For check, fix, reshard, del-node, set-timeout you can specify the host and port of any working node in the cluster.

wangpengcheng@wangpengcheng-PC:~/software/redis-cluster$ ```
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
##### 向集群中添加节点
根据新添加节点的种类， 我们需要用两种方法来将新节点添加到集群里面.  
- 如果要添加的新节点是一个主节点,那么我们需要创建一个空节点(empty node),然后将某些哈希桶移动到这个空节点里面;
- 如果要添加的新节点是一个从节点,那么我们需要将这个新节点设置为集群中某个节点的复制品(replica).  

添加新节点基本上是添加空节点然后将一些数据移入其中的过程，以防它是新的主节点，
或者告诉它设置为已知节点的副本，以防它是从属节点。

**无论添加的是那种节点， 第一步要做的总是添加一个空节点。**

###### 向集群中添加主节点
```bash
# 1.拷贝一份原来的redis实例的配置文件
wangpengcheng@wangpengcheng-PC:~/software/redis-cluster$ cp -r 7001 7007

# 2.vim的替换命令:%s 7001/7007即可
wangpengcheng@wangpengcheng-PC:~/software/redis-cluster$ vim 7007/redis.conf

# 3.启动准备添加到redis cluster中的redis实例
redis-server ./7007/redis.conf

# 4.现在我们可以像往常一样使用redis-cli，以便将节点添加到现有集群。
# 使用add-node命令将新节点的地址指定为第一个参数，并将集群中随机存在节点的地址指定为第二个参数
# 实际上，redis-cli在这方面没有什么帮助我们，它只是向节点发送了一个CLUSTER MEET消息，这也可以手动完成。
# 但是redis-cli在运行之前也会检查集群的状态，所以即使你知道内部是如何工作的，也总是通过redis-cli执行集群操作是个好主意。
redis-cli --cluster add-node 127.0.0.1:7007 127.0.0.1:7001

# 5.现在我们可以连接到新节点以查看它是否真正加入了集群
#请注意，由于此节点已连接到群集，因此它已能够正确地重定向客户端查询，并且通常是群集的一部分。然而，与其他master redis相比，它有两个特点：
#它没有数据，因为它没有分配的哈希槽。
#因为它是没有分配插槽的master设备，所以当salve redis想要成为master redis时，它不参与选举过程。

wangpengcheng@wangpengcheng-PC:~/software/redis-cluster$ redis-cli -c -h 127.0.0.1 -p 7007
127.0.0.1:7007> cluster nodes
# 查看集群新节点
redis-cli --cluster check 127.0.0.1:7007

# 6.现在可以使用resharding功能为此节点分配哈希槽redis-cli。如上所述，我们已经在前面的部分中进行了显示，这基本上是无用的，它只是一个重新分区，具有作为空节点的目标。
redis-cli --cluster reshard 127.0.0.1:7007
依次输入:5364
7007 id:ae7b80783afe71e64f52d17877d414cdacc6de23
all
yes
# 再次查看集群新节点
redis-cli --cluster check 127.0.0.1:7007

```
###### 向集群中添加从节点
```bash
redis-cli --cluster add-node 
```
##### 向集群中删除节点
删除节点也分为删除主节点和删除从节点
```bash
...
```
---

### redis Desktop Manager
```bash
deepin系统直接搜索即可安装
```
