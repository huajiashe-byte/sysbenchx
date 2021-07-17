# 修改sysbench，增加配置读写比参数 
1、增加table_columns参数控制表列数，除sysbench原有2列外，按参数个数自动增加表列数，新增表列类型为 char(5)，参数table_column默认为4，可不配置，配置需大于4
2、增加trx_read_nums/trx_write_nums参数用于控制读写比，结合sysbench原有参数point_selects，来配置在一个事务中语句的比例
 
注意：
1、point_selects用于控制execute_point_selects()方法中的查询语句数量，默认为10
2、在不指定point_selects/trx_read_nums/trx_write_nums时，默认读写比14:4，即3.5
3、在中指定了table_columns，在run中也一定需要指定相同值
 
以下各方法（oltp_read_write.lua）默认语句数：
execute_point_selects()     默认10个查询，可有sysbench原有参数point_selects控制
 
execute_simple_ranges()     默认1个查询
execute_sum_ranges()        默认1个查询
execute_order_ranges()      默认1个查询
execute_distinct_ranges()   默认1个查询
 
execute_index_updates()     默认1个update
execute_non_index_updates() 默认1个update
execute_delete_inserts()    默认1个delete，一个insert
 
 
使用
解压附件sysbench包，编译安装
将附件中独立的oltp_common.lua/oltp_read_write.lua替换原有的文件。
Prepare:
cd /usr/local/sysbench/share/sysbench/
sysbench oltp_common.lua --mysql_storage_engine=greatdb --db-driver=mysql --mysql-host=172.16.64.201 --mysql-port=16306 --mysql-user=dbtest --mysql-password=123456 --mysql-db=sbtest --threads=10 --table_size=100000 --tables=10 --table_columns=20  prepare
 
Run:
sysbench oltp_read_write.lua --db-driver=mysql --mysql-host=172.16.64.201 --mysql-port=16306 --mysql-user=dbtest --mysql-password=123456 --mysql-db=sbtest --threads=10 --table_size=500 --tables=100000 --time=1000 --histogram --report-interval=1 --db-ps-mode=disable --table_columns=20 --point_selects=1 --trx_read_nums=2 --trx_write_nums=2 run
