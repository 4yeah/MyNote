###关于浮点数比较(page97)
``` hive >select name ,salary , taxes  from employee where taxes>0.2;``` 
    ```John Doe 10000.0 0.2```
   ``` Michael Man 8000.0 0.3 ```
** 异常：查询结果出现0.2 **
原因是hive 会将条件0.2这个值保留为double类型,而 taxes 字段原为float类型继而转成double。
实际上0.2的最近似的精确值应略大于0.2,也就是说float类型的0.2实际上是0.2000001，而double的0.2实际上是0.200000000001
那么,taxes壮为double的值为0.200000100000,,数值0.2的值为0.20000000000,所以结果是这样。
有三种解决方案：
1.taxes字段类型改为double
2.taxes>0.2改为taxes>cast(0.2 as float)
3.避免使用浮点数

