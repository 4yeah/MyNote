### 什么情况下可以避免进行MapReduce ```(page94)```
- 使用本地模式：
 1. 直接读取某表
 2. 对于where语句中过滤条件只是分区字段

ps:设置属性```set hive.exec.mode.local.auto=true;``` hive会尝试使用本地模式
   最好将这个设置天剑到$HOME/.hiverc 配置文件中



### join优化e ```(page103)```
  大多数情况下，hive会对每队join连接对象启动一个mapreduce任务,
  比如a,b,c三张表关联,会产生2个mapreduce job,但如果使用的关联字段是同一个,
  那么hive会自动优化,在同一个mapreduce job中连接三张表即只产生一个mapreduce job.

hive在对每行记录进行连接操作时，它会尝试将其他表缓存起来，然后扫描最后一个表进行计算。因此用户需要保证连续查询的表的大小从左到右是依次增加的。
有疑点，可参考文章 
