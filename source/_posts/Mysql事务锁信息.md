---
title: 查询当前数据库中的事务和锁信息
date: 2019-04-19 01:09:47
categories: 
- 数据库
---

mysql锁查询字段说明

<!-- more -->

# 查询当前数据库中的事务和锁信息

## information_schema.innodb_trx
当前运行的所有事务 

Field 	| Remark
------------  | -------- |
trx_id 	|	事务ID
trx_state 	|	事务状态：
trx_started 	|	事务开始时间；
trx_requested_lock_id 	 |innodb_locks.lock_id
trx_wait_started 	|事务开始等待的时间
trx_weight 	bigint(21) | 		#
trx_mysql_thread_id 	|	事务线程ID
trx_query 	| 		具体SQL语句
trx_operation_state 	|	事务当前操作状态
trx_tables_in_use 	| 		事务中有多少个表被使用
trx_tables_locked 	| 		事务拥有多少个锁
trx_lock_structs 	| 		#
trx_lock_memory_bytes 	| 		事务锁住的内存大小（B）
trx_rows_locked 	| 		事务锁住的行数
trx_rows_modified 	| 		事务更改的行数
trx_concurrency_tickets 	| 		事务并发票数
trx_isolation_level 	| 				事务隔离级别
trx_unique_checks 	|		是否唯一性检查
trx_foreign_key_checks 	| 		是否外键检查
trx_last_foreign_key_error 	| 		最后的外键错误
trx_adaptive_hash_latched 	| 		#
trx_adaptive_hash_timeout 	| 		#

## information_schema.innodb_locks
当前出现的锁 

Field 	| Remark
--------------  | -------- |
lock_id        | 锁ID	|
lock_trx_id   |拥有锁的事务ID
lock_mode 	|	锁模式
lock_type 	|锁类型
lock_table 	|被锁的表
lock_index 	| 	被锁的索引
lock_space 	| 	被锁的表空间号
lock_page    |	被锁的页号
lock_rec 	    | 	被锁的记录号
lock_data 	| 	被锁的数据

## innodb_lock_waits.innodb_lock_waits
锁等待的对应关系

Field 	       | Remark
--------------  | -------- |
requesting_trx_id 	|		请求锁的事务ID
requested_lock_id 	|		请求锁的锁ID
blocking_trx_id 	|		当前拥有锁的事务ID
blocking_lock_id 	|		当前拥有锁的锁ID

## show full processlist
查询当前数据库中运行的进程信息（相当于正在执行的每一条sql语句的状态信息）

Field 	       | Remark
--------------  | -------- |
id | 一个标识
user| 显示当前用户，如果不是root，这 个命令就只显示你权限范围内的sql语句。
host| 显示这个语句是从哪个ip的哪个端口上发出的
db|显示 这个进程目前连接的数据库。
command|显示当前连接的执行的命令，一般就是休眠（sleep），查询（query），连接 （connect）。
time| 此这个状态持续的时间，单位是秒。
state|显示使用当前连接的sql语句的状态，只是语句执行中的某一个状态，一个sql语句，已查询为例，可能需要经过copying to tmp table，Sorting result，Sending data等状态才可以完成
info| 显示这个sql语句，因为长度有限，所以长的sql语句就显示不全，但是一个判断问题语句的重要依据。 

## state列
Field 	       | Remark
--------------  | -------- |
Checking table|正在检查数据表（这是自动的）。
Closing tables|正在将表中修改的数据刷新到磁盘中，同时正在关闭已经用完的表。这是一个很快的操作，如果不是这样的话，就应该确认磁盘空间是否已经满了或者磁盘是否正处于重负中。
Connect Out|复制从服务器正在连接主服务器。
Copying to tmp table on disk|由于临时结果集大于 tmp_table_size，正在将临时表从内存存储转为磁盘存储以此节省内存。
Creating tmp table|正在创建临时表以存放部分查询结果。
deleting from main table|服务器正在执行多表删除中的第一部分，刚删除第一个表。
deleting from reference tables|服务器正在执行多表删除中的第二部分，正在删除其他表的记录。
Flushing tables|正在执行 FLUSH TABLES，等待其他线程关闭数据表。
Killed|发送了一个kill请求给某线程，那么这个线程将会检查kill标志位，同时会放弃下一个kill请求。MySQL会在每次的主循环中检查kill标志 位，不过有些情况下该线程可能会过一小段才能死掉。如果该线程程被其他线程锁住了，那么kill请求会在锁释放时马上生效。
Locked|被其他查询锁住了。
Sending data|正在处理 SELECT 查询的记录，同时正在把结果发送给客户端。
Sorting for group|正在为 GROUP BY 做排序。
Sorting for order|正在为 ORDER BY 做排序。
Opening tables|这个过程应该会很快，除非受到其他因素的干扰。例如，在执 ALTER TABLE 或 LOCK TABLE 语句行完以前，数据表无法被其他线程打开。 正尝试打开一个表。
Removing duplicates|正在执行一个 SELECT DISTINCT 方式的查询，但是MySQL无法在前一个阶段优化掉那些重复的记录。因此，MySQL需要再次去掉重复的记录，然后再把结果发送给客户端。
Reopen table|获得了对一个表的锁，但是必须在表结构修改之后才能获得这个锁。已经释放锁，关闭数据表，正尝试重新打开数据表。
Repair by sorting|修复指令正在排序以创建索引。
Repair with keycache|修复指令正在利用索引缓存一个一个地创建新索引。它会比 Repair by sorting 慢些。
Searching rows for update|正在讲符合条件的记录找出来以备更新。它必须在 UPDATE 要修改相关的记录之前就完成了。
Sleeping|正在等待客户端发送新请求.
System lock|正在等待取得一个外部的系统锁。如果当前没有运行多个 mysqld 服务器同时请求同一个表，那么可以通过增加 –skip-external-locking参数来禁止外部系统锁。
Upgrading lock|INSERT DELAYED 正在尝试取得一个锁表以插入新记录。
Updating|正在搜索匹配的记录，并且修改它们。
User Lock|正在等待 GET_LOCK()。
Waiting for tables|该线程得到通知，数据表结构已经被修改了，需要重新打开数据表以取得新的结构。然后，为了能的重新打开数据表，必须等到所有其他线程关闭这个表。以下几种 情况下会产生这个通知：FLUSH TABLES tbl_name, ALTER TABLE, RENAME TABLE, REPAIR TABLE, ANALYZE TABLE, 或 OPTIMIZE TABLE。
waiting for handler insert|INSERT DELAYED 已经处理完了所有待处理的插入操作，正在等待新的请求。








