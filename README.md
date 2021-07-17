# 修改sysbench，增加配置读写比参数 
## 表列数参数table_columns
增加table_columns参数控制表列数，除sysbench原有2列外，按参数个数自动增加表列数，新增表列类型为 char(5)，参数table_columns默认为4，也可以不配置，配置的话需要大于4
## 读写比参数trx_read_nums/trx_write_nums
增加trx_read_nums/trx_write_nums参数用于控制读写比，结合sysbench原有参数point_selects，来配置在一个事务中语句的比例
 
## 注意：
point_selects用于控制execute_point_selects()方法中的查询语句数量，默认为10<br />
在不指定point_selects/trx_read_nums/trx_write_nums时，默认读写比14:4，即3.5<br />
在prepare中指定了table_columns，在run中也一定需要指定相同值<br />
 
## 以下各方法（oltp_read_write.lua）默认语句数：
execute_point_selects()     默认10个查询，可有sysbench原有参数point_selects控制<br />
execute_simple_ranges()     默认1个查询<br />
execute_sum_ranges()        默认1个查询<br />
execute_order_ranges()      默认1个查询<br />
execute_distinct_ranges()   默认1个查询<br />
execute_index_updates()     默认1个update<br />
execute_non_index_updates() 默认1个update<br />
execute_delete_inserts()    默认1个delete，一个insert<br />
 
 
### 使用
解压sysbench 1.0.20源码包，编译安装
将oltp_common.lua和oltp_read_write.lua替换原有的文件。
### Prepare:
cd /usr/local/sysbench/share/sysbench/
sysbench oltp_common.lua --mysql_storage_engine=innodb --db-driver=mysql --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=dbtest --mysql-password=123456 --mysql-db=sbtest --threads=10 --table_size=100000 --tables=10 --table_columns=20  prepare
 
### Run:
sysbench oltp_read_write.lua --db-driver=mysql --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-user=dbtest --mysql-password=123456 --mysql-db=sbtest --threads=10 --table_size=500 --tables=100000 --time=1000 --histogram --report-interval=1 --db-ps-mode=disable --table_columns=20 --point_selects=1 --trx_read_nums=2 --trx_write_nums=2 run
