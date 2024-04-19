# ♾️设计规范
1. 非分库分表时,使用自增id作为主键
2. 字段尽量设置为not null
3. 时间统一格式:‘YYYY-MM-DD HH:MM:SS’
4. 更新数据表记录时，必须同时更新记录对应的 gmt_modified 字段值为当前时间

# ♾️命名规范
1. 表达是与否概念的字段，必须使用 is_xxx 的方式命名，数据类型是 unsigned tinyint ( 1表示是，0表示否)。说明:任何字段如果为非负数，必须是 unsigned。
2. 数据库名、表名、字段名，都不允许出现任何大写字母!(window下mysql不区分大小写)
3. 表名不使用复数名词
4. 表必备三字段:id, is_deleted,gmt_create, gmt_modified。
5. 所有时间字段，都以 gmt_开始，后面加上动词的过去式，最后不要加上 time 单词，例如 gmt_create

# ♾️类型规范
1. id使用bigint unsigned类型(golang中对应uint64)
2. 表示状态字段(0-255)的使用 TINYINT UNSINGED，禁止使用枚举类型，注释必须清晰地说明每个枚举的含义，以及是否多选等
3. 如果存储的字符串长度几乎相等，使用 char 定长字符串类型
4. 表示boolean类型的都使用TINYINT(1)
5. 小数类型为 decimal，禁止使用 float 和 double。
6. 非负的数字类型字段，都添加上 UNSINGED
7. 时间字段使用时间日期类型，不要使用字符串类型存储，日期使用DATE类型，年使用YEAR类型，日期时间使用DATETIME

# ♾️索引规范
1. 在 varchar 字段上建立索引时，必须指定索引长度，没必要对全字段建立索引
2. 利用覆盖索引来进行查询操作，避免回表。