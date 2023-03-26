# Mysql环境搭建-CentOS
## 一 单机部署
### 1 更新系统
``` 
> sudo yum update
```
### 2 下载安装包
```
> sudo wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rp
```
### 3 准备安装包
```
> sudo rpm -Uvh mysql80-community-release-el7-3.noarch.rpm
```
### 4 安装数据
```
> sudo yum install mysql-server
```
```
Note : CPG Key失效，安装失败
The GPG keys listed for the "MySQL 8.0 Community Server" repository are already installed but they are not correct for this package.
Check that the correct key URLs are configured for this repository.

Failing package is: mysql-community-client-8.0.28-1.el7.x86_64
GPG Keys are configured as: file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql
# 解决方案：
# 1 导入新的CPG Key
> rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2022
# 2 更新服务
> yum update
```
### 5 配置数据库
#### （1）修改root密码
```
# 查询生成的默认密码，修改时需要用到旧密码
> sudo grep 'password' /var/log/mysqld.log
# 修改密码
> sudo mysql_secure_installation
```
#### （2）修改远程连接配置
```
# 没有远程连接的权限 Host xxx is not allowed to connect to this MySQL server
> mysql -u root -p
# 操作数据库
> use mysql;
> update user set host = '%' where user = 'root';
> flush privileges;
```