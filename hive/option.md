### 关于浮点数比较(page97)
``` 
hive >select name ,salary , taxes  from employee where taxes>0.2;  
        John Doe 10000.0 0.2 
        Michael Man 8000.0 0.3  
```   
**异常：查询结果出现0.2** 

原因是hive 会将条件0.2这个值保留为double类型,而 taxes 字段原为float类型继而转成double。
实际上0.2的最近似的精确值应略大于0.2,也就是说float类型的0.2实际上是0.2000001，而double的0.2实际上是0.200000000001
那么,taxes壮为double的值为0.200000100000,,数值0.2的值为0.20000000000,所以结果是这样。

有三种解决方案：
- 1.taxes字段类型改为double
- 2.taxes>0.2改为taxes>cast(0.2 as float)
- 3.避免使用浮点数

### 小技巧(page35)
hive -S -e "set" |grep word 可找到包含word这个词的hive属性

<<<<<<< HEAD
### 关联时有表没有数据会报错
报错信息：.HiveException: java.lang.NullPointerException at org.apache.hadoop.hive.ql.exec.spark.SparkMapRecordHandler.processRow(SparkMapRecordHandler.java:149) at 
可以通过设置hive参数避免
- 1.set hive.auto.convert.join=false;
- 2.set hive.vectorized.execution.mapjoin.native.enabled=false
=======

### 利用replace删除orc表字段时报错
错误信息
org.apache.hive.service.cli.HiveSQLException: Error while processing statement: FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. 
Replacing columns cannot drop columns for table temp.drop_col_table_test. 
SerDe may be incompatible at org.apache.hive.service.cli.operation.Operation.toSQLException(Operation.java:380) 
at org.apache.hive.service.cli.operation.SQLOperation.runQuery(SQLOperation.java:257) 
at org.apache.hive.service.cli.operation.SQLOperation.access$800(SQLOperation.java:91) 
at org.apache.hive.service.cli.operation.SQLOperation$BackgroundWork$1.run(SQLOperation.java:348) 
at java.security.AccessController.doPrivileged(Native Method) 
at javax.security.auth.Subject.doAs(Subject.java:422) 
at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1783) 
at org.apache.hive.service.cli.operation.SQLOperation$BackgroundWork.run(SQLOperation.java:362) 
at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:511) 
at java.util.concurrent.FutureTask.run(FutureTask.java:266) 
at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149) 
at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624) 
at java.lang.Thread.run(Thread.java:748) Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: 
Replacing columns cannot drop columns for table temp.drop_col_table_test. 
SerDe may be incompatible at org.apache.hadoop.hive.ql.exec.DDLTask.alterTableOrSinglePartition(DDLTask.java:3808) 
at org.apache.hadoop.hive.ql.exec.DDLTask.alterTable(DDLTask.java:3576) 
at org.apache.hadoop.hive.ql.exec.DDLTask.execute(DDLTask.java:390) 
at org.apache.hadoop.hive.ql.exec.Task.executeTask(Task.java:199) 
at org.apache.hadoop.hive.ql.exec.TaskRunner.runSequential(TaskRunner.java:100) 
at org.apache.hadoop.hive.ql.Driver.launchTask(Driver.java:2183) 
at org.apache.hadoop.hive.ql.Driver.execute(Driver.java:1839) 
at org.apache.hadoop.hive.ql.Driver.runInternal(Driver.java:1526) 
at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1237) 
at org.apache.hadoop.hive.ql.Driver.run(Driver.java:1232) 
at org.apache.hive.service.cli.operation.SQLOperation.runQuery(SQLOperation.java:255) ... 11 more

The only way you can delete column from existing table is by using REPLACE COLUMNS keyword. 
But this can be done only for tables with a native SerDe (DynamicSerDe, MetadataTypedColumnsetSerDe, LazySimpleSerDe and ColumnarSerDe).
Your best bet is recreating the schema. Follows the steps.

create table temp.drop_col_table_test(id int, name string)  stored as orc  ;#创建测试表
insert into temp.drop_col_table_test values(1,"A");#测试数据
select * from temp.drop_col_table_test;#查看数据
alter table   temp.drop_col_table_test replace columns(id int )#尝试删除name字段 报上述错误
alter table temp.drop_col_table_test set tblproperties('EXTERNAL'='TRUE');#将表变成外部表
drop table temp.drop_col_table_test;#因为是外部表 数据文件还在
create table temp.drop_col_table_test(id int)  stored as orc  ;#重新建表
select * from temp.drop_col_table_test;#验证结果
>>>>>>> add

