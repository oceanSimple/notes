# ♾️通用命令
## 💫连接和认证
1. ping：测试服务器是否可用
	- in：ping
	- out：PONG
2. select：选择数据库
	- 输入：select 1
	- out：OK

## 💫键操作
1. del：删除n个键
	- in：del k1 k2 k3...
	- out：1（成功删除键的个数）
2. exists：检查键是否存在
	- in：exists k1
	- out：0（不存在），1（存在）
3. expire：给键设置过期时间（秒）
	- in：expire k1 10
	- out：0（失败：不存在或已经过期了），1（成功）
4. ttl：查看键的剩余存活时间（秒）
	- in：ttl k1
	- out：正数（过期时间），-1（不存在或者没有设置过期时间），-2（已经过期删除了），0（已经过期，但还未被删除策略删除）
5. rename：重命名键
	- in：rename k1 k2
	- out：OK
6. type：返回键的值的类型
	- in：type k1
	- out：string/...


# ♾️字符串
1. set：
	- in：set k1 12
	- out：OK
2. get
	- in：get k1
	- out：12
3. incr/decr：+/-1
	- in：incr k1
	- out：13，ERROR（非数字）
4. incrby/decrby
	- in：incrby k1 10
	- out：22，ERROR
5. strlen：获取键值长度
	- in：strlen k1
	- out：字符长度，如果是数字，就输出数字的位数
6. append：追加字符串
	- in：append k1 hello
	- out：123hello
