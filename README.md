# MYSQL_Record
record the contents of mysql

# 下载安装
* UBUNTU 
````
sudo apt-get update
sudo apt-get install mysql-server
````

**安装后的相关操作**
````
# 查看 mysql 服务是否运行
systemctl status mysql.service
# 启动 mysql 服务
sudo systemctl mysql start
# 查看 mysql 版本
mysqladmin -u root -p password version
# 设置数据库编码
修改 /etc/mysql/mysql.cnf,增加以下内容：
[client]
default-character-set=utf8
[mysqld]
character-set-server=utf8
[mysql]
default-character-set=utf8
````

# 进入安全模式修改密码（如果忘记）
```
1. mysqld_safe --skip-grant-tables &
# 输入 mysql
2. mysql
# 修改密码
3. update mysql.user set authentication_string = PASSWORD('root') where User = 'root';
# 如果修改成功，退出后正常启动进行登录即可
4. quit
# 如果提示 user 表无 authentication_string 字段，可以先查看密码字段叫什么
show create table mysql.user \G;
# 找到密码字段后修改第 3 步的密码字段名称再执行即可
```

# 常用数据引擎
## Innodb

## MYISAM

## CSV
CSV 存储引擎特点：
* 以 CSV 格式进行数据存储。例如：
````
1,"a","b"
2,"a","c"
3,"bc","ac"
````
* 所有列都不能为 NULL
* 不支持索引。因此不适合大表。
* 可对数据文件直接进行编辑和保存。
# MYSQL 服务器参数
## MYSQL 配置文件读取
使用如下命令可获取当前系统下 MYSQL 读取配置文件的路径和顺序：
````
mysqld --help --verbose | grep -A 1 'Default options'

// 输出 
Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf ~/.my.cnf
````
## MYSQL 设置参数
* 设置全局参数
````
set global 参数名 = 参数值;
或者
set @@global 参数名 := 参数值;
````
* 设置会话参数
````
set session 参数名 = 参数值;
或者
set @@session.参数名 := 参数值;
````
## MSYQL 基准测试（benchmark）
mysql 基准测试：对 mysql 进行的性能测试。基准测试结果应该易于比较。

### 常见指标
* 每秒内所处理的事务数（TPS）
* 每秒内所处理的查询数（QPS）
* 响应时间
* 并发量

### 常用基准测试工具
#### 1. mysqlslap
````
常用参数,可使用 mysqlslap --help 查看详细参数：

--auto-generate-sql 系统自动生成 sql 脚本进行测试
--auto-generate-sql-add-autoincrement 在生成的表中插入自增id
--auto-generate-sql-load-type 指定使用的查询类型
--auto-generate-sql-write-number 初始化数据时生成的数据量
--concurrency 并发量
--engine 测试表使用的存储引擎，多个用逗号分割
--no-drop 一次测试结果之后不清理数据
--iterations 测试运行的次数
--number-of-queries 每条线程执行的查询数量
--debug-info 输出额外的内存和CPU统计信息
--number-int-cols 指定测试表中包含的int类型列的数量
--number-char-cols 指定测试表中包含的varchar类型的数量
--create-schema 指定用于执行测试的数据库的名称
--query 指定自定义 sql 脚本
--only-print 只生成测试脚本而不运行
````
使用示例：
````
mysqlslap --concurrency=1,5,10 --auto-generate-sql --auto-generate-sql-add-autoincrement \
--auto-generate-sql-write-number=10000 --engine=myisam,innodb --iterations=3 \
--number-of-queries=100 --number-int-cols=5 --number-char-cols=5 --create-schema=benchmarktest
````

**NOTE:** 如果在控制台使用命令 mysqlslap 提示 `mysqlslap: [ERROR] unknown variable 'default-character-set=utf8'`,则需要把`/etc/mysql/my.conf`文件中 client 配置的`default-character-set=utf8`注释掉。

#### 2 sysbench
* 安装 sysbench
````
* Debian/Ubuntu
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | sudo bash
sudo apt -y install sysbench

* RHEL/CentOS
curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.rpm.sh | sudo bash
sudo yum -y install sysbench
````
sysbench 常规命令行语法：
`sysbench [options]... [testname] [command]`
* testname : 可选的内置测试名称（如 fileio，memory，cpu等），\
或者其中一个绑定的Lua脚本名称之一，或者是一个自定义的Lua脚本路径
* command : 指定了测试会执行的动作，如下是几个典型的测试命令：
````
* prepare 执行测试需要的准备动作，如为 fileio 测试创建必要的文件或者为基准测试填充测试数据库
* run 运行测试
* cleanup 清除测试生成的临时数据
* help 展示该 testname 可以使用的命令
````
使用 `sysbench --help` 可以获取详细的命令使用方法。

````
mysql 常用参数
--msyql-db 指定基准测试数据库名
--mysql-table-engine 指定存储引擎
--oltp-tables-count 执行反而是表的数量
--oltp-table-size 执行测试表的数量
--threads 并发的线程数量
--time 最大测试时间
--report-interval 指定输出统计信息的间隔时间
--mysql-user 执行测试的用户
--mysql-password 测试用户密码
````
使用举例：
````

````






