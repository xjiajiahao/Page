title: Oracle数据库的那些坑
date: 2016-08-13 14:07:50
tags: [database]
categories: Coding
---
由于需要处理oracle数据库导出的数据，因此不得不使用oracle。在安装、使用过程中踩到很多坑。本文将列举笔者遇到的问题以及给出相应的解决方案。

### Oracle数据库部署
在[Github](https://github.com/wnameless/docker-oracle-xe-11g)上找到一个oracle xe 11g的docker镜像，部署到linux服务器上，整个过程非常简单，不需要自己下载二进制文件、解决依赖，从此跟繁琐的环境配置过程说再见。
``` bash
# 下载镜像，国内用户推荐用daocloud，加速下载镜像
docker pull wnameless/oracle-xe-11g    

# 运行容器，挂载宿主主机的文件夹
docker run -d --name=qoracle \
-p 49160:22 -p 49161:1521 \
-v ~/Fuyang_data:/root/Fuyang_data  wnameless/oracle-xe-11g
```

这样，oracle xe 11g成功部署在服务器上，可以用ssh通过49160端口登录，或者用oracle客户端通过49161端口登录。
``` bash
# 从外部通过ssh进入容器
ssh -p 49160 root@<your server ip>

# 运行oracle客户端sqlplus，默认的用户名和密码分别是`system`和`oracle`
sqlplus system/oracle
```
<!--more-->
但是sqlplus不支持readline，在容器里安装rlwrap可以解决这个问题，参见[Caelum's Blog](http://blog.cachemiss.com/articles/Using%20readline%20with%20Oracle%20SQL*Plus.pod)。
``` bash
# 先将软件源替换为国内的镜像（这里用的是清华的tuna源，强烈推荐）
sed -i "s/archive.ubuntu.com/mirrors.tuna.tsinghua.edu.cn/g" /etc/apt/sources.list

# 安装rlwrap
apt-get update
apt-get install rlwrap

# 设置bash的alias
echo "alias sqlplus='rlwrap -i -f ~/.sqlplus_history -H ~/.sqlplus_history -s 30000 sqlplus'" >> ~/.bashrc

# 然后重启bash即可
exec bash
```

### Oracle常用命令
``` sql
/* 查看数据库中的所有表格的名字和拥有者 */
select owner, table_name from dba_tables;

/* create user(schema) */
create user CLIENT_TEST identified by client_test_password;
grant connect, unlimited tablespace, resource to CLIENT_TEST;
exit

/* select example @NOTE 表格名称前要加上schema，如CLIENT_TEST.some_table */
select ID, Name from SchemaName.TableName;
```
新建用户之后才能用下面的命令导入数据
``` bash
imp system/oracle FROMUSER=<original user> TOUSER=CLIENT_TEST \
file=database.dmp log=database.log
```

### 编码设置
如果在导入imp数据时出错，并且错误信息是字段长度超限，则很有可能是编码不对，例如（导出时的编码是`ZHS16GBK`，导入时用的是`AL32UTF8`）。

**温馨提示**：如果出错后无法进入oracle，并提示`shared memory realm does not exist`，可以试试重启`oracle-xe`
``` bash
/etc/init.d/oracle-xe restart
```

编码不匹配的解决方案是将oracle服务端的默认编码改成`ZHS16GBK`，参见[fjxsunmit 的BLOG](http://fjxsunmit.blog.51cto.com/326634/600767)。
``` sql
/* 如果执行下面的语句时提示权限不足 */
/* 可以试试断开与服务器的连接，然后以oracle用户登录服务器即可 */
connect / as sysdba;

/* 查询oracle服务端字符集 */
select name,value$ from props$ where name like '%NLS%';

/* 修改编码 */
shutdown immediate;
startup mount
alter session set sql_trace=true;
alter system enable restricted session;
alter system set job_queue_processes=0;
alter system set aq_tm_processes=0;
alter database open;
set linesize 120;
alter database character set zhs16gbk;
/* 上面的命令可能出错，因为新的字符集必须是旧的字符集的超集，保证兼容性 */
/* 使用下面的命令可以跳过兼容性检查 */
ALTER DATABASE character set INTERNAL_USE zhs16gbk;
shutdown immediate;
STARTUP
select name,value$ from props$ where name like '%NLS%';
/* 再次检查字符集以确认是否成功修改编码 */

```

编码改过来之后即可正常导入dmp数据，但是打印表格时中文显示仍然可能是`???`，这是由于系统自身的编码问题，笔者多次尝试将ubuntu系统默认的`locale`改为`zh_CN.GBK`，但是没有成功，最后在windows系统通过putty连接容器，修改putty的编码后才能正常显示中文。

### 将查询结果保存到文件
只要写两个sql脚本就能实现将查询结果输出到文本文件或者Excel文件中，参考自[VeryWhy](http://verywhy.com/7097/oracle-sqlplus%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C%E8%BE%93%E5%87%BA%E5%88%B0%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6%E6%88%96excel%E6%96%87%E4%BB%B6%E4%B8%AD%E7%9A%84%E6%96%B9%E6%B3%95)。

第一个脚本是通用的，用以设置输出格式。

``` sql
/* GetData.sql */
set term off
set heading off
set verify off
set echo off
spool /path/to/result.csv
@Select.sql
spool off
exit
```

第二个脚本则是普通的sql语句，以逗号为分隔符：
``` sql
select ID || ',' || Name from SchemaName.TableName;
```
