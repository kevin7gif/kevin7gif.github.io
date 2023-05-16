DQL语句的执行顺序

```sql
select		5（字段名）
	...
from		1（表名）      
	...
where		2（条件）
	...
group by	3（按某个字段名或某些字段名分组）
	...
having		4（对分组后的数据进行再次过滤）
	...
order by	6（排序）
	...
```

分组函数：count（计数），avg（平均数），sum（总和），max（最大数），min（最小数）

**分组函数一般和group by连用，where后面不能跟分组函数。因为按照DQL语句执行顺序，where是在group by前面执行的**