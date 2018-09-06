## MySQL服务器的配置参数
### 获取MySQL配置信息路径
1. 命令行参数
    mysqld_safe --datadir=/data/sql_data
2. 配置文件

### 内存配置相关参数
1. 确定可以使用的内存上限
2. 确定MySQL的每个连接使用的内存

### IO相关的配置参数
1. Innodb IO配置
  Innodb_log_file_size 控制单个日志的大小
  Innodb_log_files_in_group 控制事务日志的个数
  Innodb_log_buffer_size 控制日志缓冲区的大小
  Innodb_flush_log_at_trx_commit 日志刷新到磁盘中的频率
    * 0:每秒进行一次log写入cache，并flush log到磁盘
    * 1[默认]： 每次事务提交时写入cache并flush log到磁盘，性能较差
    * 2[建议]： 每次事务提交执行log数据写入到cache，每秒执行一次flush log到磁盘
    
2. MySIAM IO配置
delay_key_write
    off:每次写操作后刷新缓存中的数据到磁盘, 性能较差
    on：只对指定了delay_key_write选项的表使用延迟刷新
    ALL：对所有MYISAM表都使用延迟键写入