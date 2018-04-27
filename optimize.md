## 什么情况下可以避免进行MapReduce (94页 第6章)
本地模式：- 1.直接读取某表
	 - 2.对于where语句中过滤条件只是分区字段
ps:设置属性```set hive.exec.mode.local.auto=true;``` hive会尝试使用本地模式
   最好将这个设置天剑到$HOME/.hiverc 配置文件中



