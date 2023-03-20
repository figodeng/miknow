# Redis环境搭建-CentOS
## 一、常用命令
### (1) 查询redis相关进程
> ps -aef | grep redis
### (2) redis服务
> systemctl status redis \
> systemctl restart redis
### (3) 跨机器文件传输
> scp -r /usr/local/redis-7.0.9 root@hecs:/usr/local/redis-7.0.9
## 二、单机部署
### (1) add repository
> sudo yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
### (2) install
> sudo yum --enablerepo=remi install redis
### (3) start service
> sudo systemctl enable --now redis
### (4) config
``` 
sudo vi /etc/redis.conf

# allow others to connect 
bind * -::* 
# set password
requirepass password
``` 
### (5) config forward

> Open port 6379

## 三、单机部署-通过源代码安装
```
(1) wget http://download.redis.io/releases/redis-7.0.9.tar.gz

(2) tar -xvf redis-7.0.9.tar.gz

(3) mv redis-7.0.9 /usr/local

(4) cd /usr/local/redis-5.0.8

(5) make 编译

(6) make install 编译后安装

(7)redis-server /usr/local/redis-7.0.9/redis.conf --daemonize yes

Option : 通过以下命令copy编译后文件，如果带宽小copy比较慢，建议用以上流程

scp -r /usr/local/redis-7.0.9 root@hecs-30471:/usr/local/redis-7.0.9
```
## 四、高可用集群 - non-cluster模式

### (1) 主从模式 - 从节点配置
```
SLAVEOF m1 6379
CONFIG SET MASTERAUTH password
```
### (2) sentinel模式 - 部署sentinel集群
```
通过sentinel实现failover，保证集群可用
```
## 五、高可用集群- cluster模式

数据分片存储，方便横向扩展，每个哈希槽对应一个小集群(类似于sentinel模式)

### (1) 启动配置集群每个node
```
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
```
### (2) 每个node需要统一密码
```
# allow others to connect
bind * -::*
# set password
requirepass password
```
### (3) 创建集群
```
redis-cli -a password —cluster create m1:6379 m2:6379 m3:6379 s1:6379 s2:6379 s3:6379 --cluster-replicas 1

Note: master至少3个节点，官方建议集群6个节点，master和slave是1比1
```