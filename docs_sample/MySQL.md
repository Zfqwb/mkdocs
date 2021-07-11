# MySQL 参考手册

> Reference Manual

## day01

### 01操作数据库和数据表

#### 	查询所有数据库

```sql
/*
	标准语法：
		SHOW DATABASES;
*/
```

#### 查询某个数据库的创建语句

```sql
/*
	标准语法：
		SHOW CREATE DATABASE 数据库名称;
*/
```

#### 创建数据库

```sql
/*
	标准语法：
		CREATE DATABASE 数据库名称;
*/
```

#### 创建数据库，判断、如果不存在则创建

```sql
/*
	标准语法：
		CREATE DATABASE IF NOT EXISTS 数据库名称;
*/
```

#### 创建数据库、并指定字符集

```sql
/*
	标准语法：
		CREATE DATABASE 数据库名称 CHARACTER SET 字符集名称;
*/
```



####  查看数据库的字符集

```sql
SHOW CREATE DATABASE 数据库名称;
```

#### 修改数据库的字符集

```sql
/*	
	标准语法：
		ALTER DATABASE 数据库名称 CHARACTER SET 字符集名称;
*/
```

#### 删除数据库

```sql
/*
	标准语法：
		DROP DATABASE 数据库名称;
*/
```

#### 删除数据库，判断、如果存在则删除

```sql
/*
	标准语法：
		DROP DATABASE IF EXISTS 数据库名称;
*/
```

#### 使用数据库

```sql
/*
	标准语法：
		USE 数据库名称;
*/
```

#### 查询当前使用的数据库

```sql
/*
	标准语法：
		SELECT DATABASE();
*/
```



#### 查询所有数据表

```sql
/*
	标准语法：
		SHOW TABLES;
*/
```

#### 查询表结构

```sql
/*
	标准语法：
		DESC 表名;
*/
```

#### 查询数据表的字符集

```sql
/*
	标准语法：
		SHOW TABLE STATUS FROM 数据库名称 LIKE '表名';
*/
```

#### 创建数据表

```sql
/*
	标准语法：
		CREATE TABLE 表名(
			列名 数据类型 约束,
			列名 数据类型 约束,
			...
			列名 数据类型 约束
		);
*/
```

#### 修改表名

```sql
/*
	标准语法：
		ALTER TABLE 旧表名 RENAME TO 新表名;
*/
```

#### 修改表的字符集

```sql
/*
	标准语法：
		ALTER TABLE 表名 CHARACTER SET 字符集名称;
*/
```

#### 给表添加列

```
/*
	标准语法：
		ALTER TABLE 表名 ADD 列名 数据类型;
*/
```

#### 修改表中列的数据类型

```
/*
	标准语法：
		ALTER TABLE 表名 MODIFY 列名 数据类型;
*/
```

#### 修改表中列的名称和数据类型

```
/*
	标准语法：
		ALTER TABLE 表名 CHANGE 旧列名 新列名 数据类型;
*/
```

#### 删除表中的列

```
/*
	标准语法：
		ALTER TABLE 表名 DROP 列名;
*/
```

#### 删除表

```
/*
	标准语法：
		DROP TABLE 表名;
*/
```

#### 删除表，判断、如果存在则删除

```
/*
	标准语法：
		DROP TABLE IF EXISTS 表名;
*/
```



### 02表数据的增删改

#### 给指定列添加数据

```
/*
	标准语法：
		INSERT INTO 表名(列名1,列名2,...) VALUES (值1,值2,...);
*/
```

#### 给全部列添加数据

```
/*
	标准语法：
		INSERT INTO 表名 VALUES (值1,值2,值3,...);
*/
```

#### 批量添加所有列数据

```
/*
	标准语法：
		INSERT INTO 表名 VALUES (值1,值2,值3,...),(值1,值2,值3,...),(值1,值2,值3,...);
*/
```
#### 修改表数据

```
/*
	标准语法：
		UPDATE 表名 SET 列名1 = 值1,列名2 = 值2,... [where 条件];
*/
```

#### 删除表数据

```
/*
	标准语法：
		DELETE FROM 表名 [WHERE 条件];
*/
```
### 03表数据的查询

#### 查询全部数据

```
/*
	标准语法：
		SELECT * FROM 表名;
*/
```

#### 查询指定列

```
/*
	标准语法：
		SELECT 列名1,列名2,... FROM 表名;
*/
```

#### 去除重复查询

```
/*
	标准语法：
		SELECT DISTINCT 列名1,列名2,... FROM 表名;
*/
```




#### 计算列的值

```
/*
	标准语法：
		SELECT 列名1 运算符(+ - * /) 列名2 FROM 表名;
		如果某一列为null，可以进行替换
		ifnull(表达式1,表达式2)
		表达式1：想替换的列
		表达式2：想替换的值
*/
```

`example`

-- 查询商品名称和库存，库存数量在原有基础上加10

```
SELECT NAME,stock+10 FROM product;
```

-- 查询商品名称和库存，库存数量在原有基础上加10。进行null值判断

```
SELECT NAME,IFNULL(stock,0)+10 FROM product;
```

#### 起别名

```
/*
	标准语法：
		SELECT 列名1,列名2,... AS 别名 FROM 表名;
*/
```

*as可以省略*

#### 条件查询

```
/*
	标准语法：
		SELECT 列名列表 FROM 表名 WHERE 条件;
*/
```

`example`

```
-- 查询库存大于20的商品信息
SELECT * FROM product WHERE stock > 20;

-- 查询品牌为华为的商品信息
SELECT * FROM product WHERE brand='华为';

-- 查询金额在4000 ~ 6000之间的商品信息
SELECT * FROM product WHERE price >= 4000 AND price <= 6000;
SELECT * FROM product WHERE price BETWEEN 4000 AND 6000;


-- 查询库存为14、30、23的商品信息
SELECT * FROM product WHERE stock=14 OR stock=30 OR stock=23;
SELECT * FROM product WHERE stock IN(14,30,23);

-- 查询库存为null的商品信息
SELECT * FROM product WHERE stock IS NULL;

-- 查询库存不为null的商品信息
SELECT * FROM product WHERE stock IS NOT NULL;


-- 查询名称以小米为开头的商品信息
SELECT * FROM product WHERE NAME LIKE '小米%';

-- 查询名称第二个字是为的商品信息
SELECT * FROM product WHERE NAME LIKE '_为%';

-- 查询名称为四个字符的商品信息
SELECT * FROM product WHERE NAME LIKE '____';

-- 查询名称中包含电脑的商品信息
SELECT * FROM product WHERE NAME LIKE '%电脑%';
```

#### 聚合函数

```
/*
	标准语法：
		SELECT 函数名(列名) FROM 表名 [WHERE 条件];
*/
```

`example`

```
-- 计算product表中总记录条数
SELECT COUNT(*) FROM product;

-- 获取最高价格
SELECT MAX(price) FROM product;


-- 获取最低库存
SELECT MIN(stock) FROM product;


-- 获取总库存数量
SELECT SUM(stock) FROM product;


-- 获取品牌为苹果的总库存数量
SELECT SUM(stock) FROM product WHERE brand='苹果';

-- 获取品牌为小米的平均商品价格
SELECT AVG(price) FROM product WHERE brand='小米';
```

#### 排序查询

```
/*
	标准语法：
		SELECT 列名 FROM 表名 [WHERE 条件] ORDER BY 列名1 排序方式1,列名2 排序方式2;
*/
```

`example`

```
-- 按照库存升序排序
SELECT * FROM product ORDER BY stock ASC;

-- 查询名称中包含手机的商品信息。按照金额降序排序
SELECT * FROM product WHERE NAME LIKE '%手机%' ORDER BY price DESC;

-- 按照金额升序排序，如果金额相同，按照库存降序排列
SELECT * FROM product ORDER BY price ASC,stock DESC;


```

#### 	分组查询

```
/*
	标准语法：
		SELECT 列名 FROM 表名 [WHERE 条件] GROUP BY 分组列名 [HAVING 分组后条件过滤] [ORDER BY 排序列名 排序方式];
*/
```

#### 分页查询

```
/*
	标准语法：
		SELECT 列名 FROM 表名 
		[WHERE 条件] 
		[GROUP BY 分组列名]
		[HAVING 分组后条件过滤] 
		[ORDER BY 排序列名 排序方式] 
		LIMIT 当前页数,每页显示的条数;
	

	LIMIT 当前页数,每页显示的条数;
	公式：当前页数 = (当前页数-1) * 每页显示的条数

*/
```

`example`

```
-- 每页显示3条数据

-- 第1页  当前页数=(1-1) * 3
SELECT * FROM product LIMIT 0,3;

-- 第2页  当前页数=(2-1) * 3
SELECT * FROM product LIMIT 3,3;

-- 第3页  当前页数=(3-1) * 3
SELECT * FROM product LIMIT 6,3;
```



### 04约束

#### 主键约束

```
-- 创建学生表(编号、姓名、年龄)  编号设为主键
CREATE TABLE student(
	id INT PRIMARY KEY,
	NAME VARCHAR(30),
	age INT
);

-- 查询学生表的详细信息
DESC student;

-- 添加数据
INSERT INTO student VALUES (1,'张三',23);
INSERT INTO student VALUES (2,'李四',24);

-- 删除主键
ALTER TABLE student DROP PRIMARY KEY;

-- 建表后单独添加主键约束
ALTER TABLE student MODIFY id INT PRIMARY KEY;
```

#### 主键自增约束

```
-- 创建学生表(编号、姓名、年龄)  编号设为主键自增
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(30),
	age INT
);

-- 查询学生表的详细信息
DESC student;

-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23),(NULL,'李四',24);

-- 删除自增约束
ALTER TABLE student MODIFY id INT;
INSERT INTO student VALUES (NULL,'张三',23);

-- 建表后单独添加自增约束
ALTER TABLE student MODIFY id INT AUTO_INCREMENT;
```

#### 唯一约束

```
-- 创建学生表(编号、姓名、年龄)  编号设为主键自增，年龄设为唯一
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(30),
	age INT UNIQUE
);

-- 查询学生表的详细信息
DESC student;

-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23);
INSERT INTO student VALUES (NULL,'李四',23);

-- 删除唯一约束
ALTER TABLE student DROP INDEX age;

-- 建表后单独添加唯一约束
ALTER TABLE student MODIFY age INT UNIQUE;
```

#### 非空约束

```
-- 创建学生表(编号、姓名、年龄)  编号设为主键自增，姓名设为非空，年龄设为唯一
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(30) NOT NULL,
	age INT UNIQUE
);

-- 查询学生表的详细信息
DESC student;

-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23);

-- 删除非空约束
ALTER TABLE student MODIFY NAME VARCHAR(30);
INSERT INTO student VALUES (NULL,NULL,25);

-- 建表后单独添加非空约束
ALTER TABLE student MODIFY NAME VARCHAR(30) NOT NULL;
```



## day02

### 01约束

#### 外键约束

```
/*
	标准语法：
		CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名)
*/
```

#### 删除外键约束

```
/*
	标准语法：
		ALTER TABLE 表名 DROP FOREIGN KEY 外键名;
*/
```



#### 建表后单独添加外键约束

```
/*
	标准语法：
		ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名);
*/
```

`外键约束example`

```
-- 创建db2数据库
CREATE DATABASE db2;

-- 使用db2数据库
USE db2;

/*
	外键约束
	标准语法：
		CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主表主键列名)
*/
-- 建表时添加外键约束
-- 创建user用户表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,    -- id
	NAME VARCHAR(20) NOT NULL             -- 姓名
);
-- 添加用户数据
INSERT INTO USER VALUES (NULL,'张三'),(NULL,'李四');


-- 创建orderlist订单表
CREATE TABLE orderlist(
	id INT PRIMARY KEY AUTO_INCREMENT,    -- id
	number VARCHAR(20) NOT NULL,          -- 订单编号
	uid INT,			      -- 外键列
	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
);
-- 添加订单数据
INSERT INTO orderlist VALUES (NULL,'hm001',1),(NULL,'hm002',1),
(NULL,'hm003',2),(NULL,'hm004',2);


-- 添加一个订单，但是没有真实用户。添加失败
INSERT INTO orderlist VALUES (NULL,'hm005',3);

-- 删除李四用户。删除失败
DELETE FROM USER WHERE NAME='李四';



/*
	删除外键约束
	标准语法：
		ALTER TABLE 表名 DROP FOREIGN KEY 外键名;
*/
-- 删除外键约束
ALTER TABLE orderlist DROP FOREIGN KEY ou_fk1;



/*
	建表后单独添加外键约束
	标准语法：
		ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名);
*/
-- 添加外键约束
ALTER TABLE orderlist ADD CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id);
```



#### 外键级联操作

```
/*
	添加外键约束，同时添加级联更新  标准语法：
	ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名) 
	ON UPDATE CASCADE;
	
	添加外键约束，同时添加级联删除  标准语法：
	ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名) 
	ON DELETE CASCADE;
	
	添加外键约束，同时添加级联更新和级联删除  标准语法：
	ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名) 
	ON UPDATE CASCADE ON DELETE CASCADE;
*/
```

`外键级联操作example`

```

-- 删除外键约束
ALTER TABLE orderlist DROP FOREIGN KEY ou_fk1;

-- 添加外键约束，同时添加级联更新和级联删除
ALTER TABLE orderlist ADD CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
ON UPDATE CASCADE ON DELETE CASCADE;


-- 将李四这个用户的id修改为3,订单表中的uid也自动修改
UPDATE USER SET id=3 WHERE id=2;

-- 将李四这个用户删除,订单表中的该用户所属的订单也自动删除
DELETE FROM USER WHERE id=3;
```



### 02多表操作

#### 表关系一对一

```
-- 创建db3数据库
CREATE DATABASE db3;

-- 使用db3数据库
USE db3;

-- 创建person表
CREATE TABLE person(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)                        -- 姓名
);
-- 添加数据
INSERT INTO person VALUES (NULL,'张三'),(NULL,'李四');

-- 创建card表
CREATE TABLE card(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	number VARCHAR(20) UNIQUE NOT NULL,	-- 身份证号
	pid INT UNIQUE,                         -- 外键列
	CONSTRAINT cp_fk1 FOREIGN KEY (pid) REFERENCES person(id)
);
-- 添加数据
INSERT INTO card VALUES (NULL,'12345',1),(NULL,'56789',2);
```



#### 表关系一对多

```
-- 创建user表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)                        -- 姓名
);
-- 添加数据
INSERT INTO USER VALUES (NULL,'张三'),(NULL,'李四');

-- 创建orderlist表
CREATE TABLE orderlist(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	number VARCHAR(20),                     -- 订单编号
	uid INT,				-- 外键列
	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
);
-- 添加数据
INSERT INTO orderlist VALUES (NULL,'hm001',1),(NULL,'hm002',1),(NULL,'hm003',2),(NULL,'hm004',2);



/*
	商品分类和商品
*/
-- 创建category表
CREATE TABLE category(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(10)                        -- 分类名称
);
-- 添加数据
INSERT INTO category VALUES (NULL,'手机数码'),(NULL,'电脑办公');

-- 创建product表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(30),			-- 商品名称
	cid INT,				-- 外键列
	CONSTRAINT pc_fk1 FOREIGN KEY (cid) REFERENCES category(id)
);
-- 添加数据
INSERT INTO product VALUES (NULL,'华为P30',1),(NULL,'小米note3',1),
(NULL,'联想电脑',2),(NULL,'苹果电脑',2);
```



#### 表关系多对多

```
-- 创建student表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(20)			-- 学生姓名
);
-- 添加数据
INSERT INTO student VALUES (NULL,'张三'),(NULL,'李四');

-- 创建course表
CREATE TABLE course(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	NAME VARCHAR(10)			-- 课程名称
);
-- 添加数据
INSERT INTO course VALUES (NULL,'语文'),(NULL,'数学');


-- 创建中间表
CREATE TABLE stu_course(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 主键id
	sid INT,  -- 用于和student表中的id进行外键关联
	cid INT,  -- 用于和course表中的id进行外键关联
	CONSTRAINT sc_fk1 FOREIGN KEY (sid) REFERENCES student(id), -- 添加外键约束
	CONSTRAINT sc_fk2 FOREIGN KEY (cid) REFERENCES course(id)   -- 添加外键约束
);
-- 添加数据
INSERT INTO stu_course VALUES (NULL,1,1),(NULL,1,2),(NULL,2,1),(NULL,2,2);
```

#### `多表查询数据准备`

```
-- 创建db4数据库
CREATE DATABASE db4;
-- 使用db4数据库
USE db4;

-- 创建user表
CREATE TABLE USER(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 用户id
	NAME VARCHAR(20),			-- 用户姓名
	age INT                                 -- 用户年龄
);
-- 添加数据
INSERT INTO USER VALUES (1,'张三',23);
INSERT INTO USER VALUES (2,'李四',24);
INSERT INTO USER VALUES (3,'王五',25);
INSERT INTO USER VALUES (4,'赵六',26);


-- 订单表
CREATE TABLE orderlist(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 订单id
	number VARCHAR(30),			-- 订单编号
	uid INT,    -- 外键字段
	CONSTRAINT ou_fk1 FOREIGN KEY (uid) REFERENCES USER(id)
);
-- 添加数据
INSERT INTO orderlist VALUES (1,'hm001',1);
INSERT INTO orderlist VALUES (2,'hm002',1);
INSERT INTO orderlist VALUES (3,'hm003',2);
INSERT INTO orderlist VALUES (4,'hm004',2);
INSERT INTO orderlist VALUES (5,'hm005',3);
INSERT INTO orderlist VALUES (6,'hm006',3);
INSERT INTO orderlist VALUES (7,'hm007',NULL);


-- 商品分类表
CREATE TABLE category(
	id INT PRIMARY KEY AUTO_INCREMENT,  -- 商品分类id
	NAME VARCHAR(10)                    -- 商品分类名称
);
-- 添加数据
INSERT INTO category VALUES (1,'手机数码');
INSERT INTO category VALUES (2,'电脑办公');
INSERT INTO category VALUES (3,'烟酒茶糖');
INSERT INTO category VALUES (4,'鞋靴箱包');


-- 商品表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,   -- 商品id
	NAME VARCHAR(30),                    -- 商品名称
	cid INT, -- 外键字段
	CONSTRAINT cp_fk1 FOREIGN KEY (cid) REFERENCES category(id)
);
-- 添加数据
INSERT INTO product VALUES (1,'华为手机',1);
INSERT INTO product VALUES (2,'小米手机',1);
INSERT INTO product VALUES (3,'联想电脑',2);
INSERT INTO product VALUES (4,'苹果电脑',2);
INSERT INTO product VALUES (5,'中华香烟',3);
INSERT INTO product VALUES (6,'玉溪香烟',3);
INSERT INTO product VALUES (7,'计生用品',NULL);


-- 中间表
CREATE TABLE us_pro(
	upid INT PRIMARY KEY AUTO_INCREMENT,  -- 中间表id
	uid INT, -- 外键字段。需要和用户表的主键产生关联
	pid INT, -- 外键字段。需要和商品表的主键产生关联
	CONSTRAINT up_fk1 FOREIGN KEY (uid) REFERENCES USER(id),
	CONSTRAINT up_fk2 FOREIGN KEY (pid) REFERENCES product(id)
);
-- 添加数据
INSERT INTO us_pro VALUES (NULL,1,1);
INSERT INTO us_pro VALUES (NULL,1,2);
INSERT INTO us_pro VALUES (NULL,1,3);
INSERT INTO us_pro VALUES (NULL,1,4);
INSERT INTO us_pro VALUES (NULL,1,5);
INSERT INTO us_pro VALUES (NULL,1,6);
INSERT INTO us_pro VALUES (NULL,1,7);
INSERT INTO us_pro VALUES (NULL,2,1);
INSERT INTO us_pro VALUES (NULL,2,2);
INSERT INTO us_pro VALUES (NULL,2,3);
INSERT INTO us_pro VALUES (NULL,2,4);
INSERT INTO us_pro VALUES (NULL,2,5);
INSERT INTO us_pro VALUES (NULL,2,6);
INSERT INTO us_pro VALUES (NULL,2,7);
INSERT INTO us_pro VALUES (NULL,3,1);
INSERT INTO us_pro VALUES (NULL,3,2);
INSERT INTO us_pro VALUES (NULL,3,3);
INSERT INTO us_pro VALUES (NULL,3,4);
INSERT INTO us_pro VALUES (NULL,3,5);
INSERT INTO us_pro VALUES (NULL,3,6);
INSERT INTO us_pro VALUES (NULL,3,7);
INSERT INTO us_pro VALUES (NULL,4,1);
INSERT INTO us_pro VALUES (NULL,4,2);
INSERT INTO us_pro VALUES (NULL,4,3);
INSERT INTO us_pro VALUES (NULL,4,4);
INSERT INTO us_pro VALUES (NULL,4,5);
INSERT INTO us_pro VALUES (NULL,4,6);
INSERT INTO us_pro VALUES (NULL,4,7);
```

#### 内连接查询

##### 	显示内连接

```
/*
	标准语法：
		SELECT 列名 FROM 表名1 [INNER] JOIN 表名2 ON 关联条件;
*/
```

##### 隐式内连接

```
/*
	标准语法：
		SELECT 列名 FROM 表名1,表名2 WHERE 关联条件;
*/
```

#### 外连接查询

##### 	左外连接

```
/*
	标准语法：
		SELECT 列名 FROM 表名1 LEFT [OUTER] JOIN 表名2 ON 条件;
*/
```

##### 	右外连接

```
/*
	标准语法：
		SELECT 列名 FROM 表名1 RIGHT [OUTER] JOIN 表名2 ON 条件;
*/
```

#### 子查询

```
/*
	结果是单行单列的
	标准语法：
		SELECT 列名 FROM 表名 WHERE 列名=(SELECT 列名 FROM 表名 [WHERE 条件]);
*/
```

-- 查询年龄最高的用户姓名
SELECT MAX(age) FROM USER;
SELECT NAME,age FROM USER WHERE age=(SELECT MAX(age) FROM USER);

```
/*
	结果是多行单列的
	标准语法：
		SELECT 列名 FROM 表名 WHERE 列名 [NOT] IN (SELECT 列名 FROM 表名 [WHERE 条件]); 
*/
```

-- 查询张三和李四的订单信息
SELECT * FROM orderlist WHERE uid IN (1,2);
SELECT id FROM USER WHERE NAME IN ('张三','李四');
SELECT * FROM orderlist WHERE uid IN (SELECT id FROM USER WHERE NAME IN ('张三','李四'));

```
/*
	结果是多行多列的
	标准语法：
		SELECT 列名 FROM 表名 [别名],(SELECT 列名 FROM 表名 [WHERE 条件]) [别名] [WHERE 条件];
*/
```

-- 查询订单表中id大于4的订单信息和所属用户信息
SELECT * FROM orderlist WHERE id > 4;
SELECT
	u.name,
	o.number
FROM
	USER u,
	(SELECT * FROM orderlist WHERE id > 4) o
WHERE
	o.uid=u.id;

#### 自关联查询

```
-- 创建员工表
CREATE TABLE employee(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 员工编号
	NAME VARCHAR(20),			-- 员工姓名
	mgr INT,				-- 上级编号
	salary DOUBLE				-- 员工工资
);
-- 添加数据
INSERT INTO employee VALUES (1001,'孙悟空',1005,9000.00),
(1002,'猪八戒',1005,8000.00),
(1003,'沙和尚',1005,8500.00),
(1004,'小白龙',1005,7900.00),
(1005,'唐僧',NULL,15000.00),
(1006,'武松',1009,7600.00),
(1007,'李逵',1009,7400.00),
(1008,'林冲',1009,8100.00),
(1009,'宋江',NULL,16000.00);


-- 查询所有员工的姓名及其直接上级的姓名，没有上级的员工也需要查询
/*
分析
	员工信息 employee表
	条件：employee.mgr = employee.id
	查询左表的全部数据，和左右两张表有交集部分数据，左外连接
*/
SELECT
	e1.id,
	e1.name,
	e1.mgr,
	e2.id,
	e2.name
FROM
	employee e1
LEFT OUTER JOIN
	employee e2
ON
	e1.mgr = e2.id;
```



#### 多表查询练习

```
-- 1.查询用户的编号、姓名、年龄。订单编号
/*
分析
	用户的编号、姓名、年龄  user表      订单编号 orderlist表
	条件：user.id=orderlist.uid
*/
SELECT
	u.id,
	u.name,
	u.age,
	o.number
FROM
	USER u,
	orderlist o
WHERE
	u.id=o.uid;



-- 2.查询所有的用户。用户的编号、姓名、年龄。订单编号
/*
分析
	用户的编号、姓名、年龄  user表    订单编号 orderlist表
	条件：user.id=orderlist.uid
	查询所有的用户，左外连接
*/
SELECT
	u.id,
	u.name,
	u.age,
	o.number
FROM
	USER u
LEFT OUTER JOIN
	orderlist o
ON
	u.id=o.uid;



-- 3.查询所有的订单。用户的编号、姓名、年龄。订单编号
/*
分析
	用户的编号、姓名、年龄 user表    订单编号 orderlist表
	条件：user.id=orderlist.uid
	查询所有的订单，右外连接
*/
SELECT
	u.id,
	u.name,
	u.age,
	o.number
FROM
	USER u
RIGHT OUTER JOIN
	orderlist o
ON
	u.id=o.uid;

	

-- 4.查询用户年龄大于23岁的信息。显示用户的编号、姓名、年龄。订单编号
/*
分析
	用户的编号、姓名、年龄 user表    订单编号 orderlist表
	条件：user.id=orderlist.uid AND user.age > 23
*/
SELECT
	u.id,
	u.name,
	u.age,
	o.number
FROM
	USER u,
	orderlist o
WHERE
	u.id=o.uid
	AND
	u.age > 23;


-- 5.查询张三和李四用户的信息。显示用户的编号、姓名、年龄。订单编号
/*
分析
	用户的编号、姓名、年龄 user表   订单编号 orderlist表
	条件：user.id=orderlist.uid AND user.name IN ('张三','李四')
*/
SELECT
	u.id,
	u.name,
	u.age,
	o.number
FROM
	USER u,
	orderlist o
WHERE
	u.id=o.uid
	AND
	u.name IN ('张三','李四');


-- 6.查询商品分类的编号、分类名称。分类下的商品名称
/*
分析
	商品分类的编号、分类名称 category表    商品名称 product表
	条件：category.id=product.cid
*/
SELECT
	c.id,
	c.name,
	p.name
FROM
	category c,
	product p
WHERE
	c.id=p.cid;


-- 7.查询所有的商品分类。商品分类的编号、分类名称。分类下的商品名称
/*
分析
	商品分类的编号、分类名称 category表    商品名称 product表
	条件：category.id=product.cid
	查询所有的商品分类，左外连接
*/
SELECT
	c.id,
	c.name,
	p.name
FROM
	category c
LEFT OUTER JOIN
	product p
ON
	c.id=p.cid;


-- 8.查询所有的商品信息。商品分类的编号、分类名称。分类下的商品名称
/*
分析
	商品分类的编号、分类名称  category表   商品名称 product表
	条件：category.id=product.cid
	查询所有的商品信息，右外连接
*/
SELECT
	c.id,
	c.name,
	p.name
FROM
	category c
RIGHT OUTER JOIN
	product p
ON
	c.id=p.cid;



-- 9.查询所有的用户和该用户能查看的所有的商品。显示用户的编号、姓名、年龄。商品名称
/*
分析
	用户的编号、姓名、年龄 user表   商品名称 product表    中间表 us_pro
	条件：us_pro.uid=user.id AND us_pro.pid=product.id
*/
SELECT
	u.id,
	u.name,
	u.age,
	p.name
FROM
	USER u,
	product p,
	us_pro up
WHERE
	up.uid=u.id
	AND
	up.pid=p.id;



-- 10.查询张三和李四这两个用户可以看到的商品。显示用户的编号、姓名、年龄。商品名称
/*
分析
	用户的编号、姓名、年龄 user表   商品名称 product表   中间表 us_pro
	条件：us_pro.uid=user.id AND us_pro.pid=product.id AND user.name IN ('张三','李四') 
*/
SELECT
	u.id,
	u.name,
	u.age,
	p.name
FROM
	USER u,
	product p,
	us_pro up
WHERE
	up.uid=u.id
	AND
	up.pid=p.id
	AND
	u.name IN ('张三','李四');
```



### 03视图

#### 视图_数据准备

```
-- 创建db5数据库
CREATE DATABASE db5;

-- 使用db5数据库
USE db5;

-- 创建country表
CREATE TABLE country(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 国家id
	NAME VARCHAR(30)                        -- 国家名称
);
-- 添加数据
INSERT INTO country VALUES (NULL,'中国'),(NULL,'美国'),(NULL,'俄罗斯');

-- 创建city表
CREATE TABLE city(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 城市id
	NAME VARCHAR(30),			-- 城市名称
	cid INT, -- 外键列。关联country表的主键列id
	CONSTRAINT cc_fk1 FOREIGN KEY (cid) REFERENCES country(id)  -- 添加外键约束
);
-- 添加数据
INSERT INTO city VALUES (NULL,'北京',1),(NULL,'上海',1),(NULL,'纽约',2),(NULL,'莫斯科',3);
```



####   创建视图

```
/*

  标准语法

    CREATE VIEW 视图名称 [(列名列表)] AS 查询语句;

*/
```

`example`

```
-- 创建city_country视图，保存城市和国家的信息(使用指定列名)

CREATE VIEW city_country (city_id,city_name,country_name) AS

SELECT

  c1.id,

  c1.name,

  c2.name

FROM

  city c1,

  country c2

WHERE

  c1.cid=c2.id;

```



####   查询视图

```
/*

  标准语法

    SELECT * FROM 视图名称;

*/
```

`example`

```
-- 查询视图

SELECT * FROM city_country;
```

## day03

### 01视图

#### 数据准备

```
-- 创建db7数据库
CREATE DATABASE db7;

-- 使用db7数据库
USE db7;

-- 创建country表
CREATE TABLE country(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 国家id
	country_name VARCHAR(30)                -- 国家名称
);
-- 添加数据
INSERT INTO country VALUES (NULL,'中国'),(NULL,'美国'),(NULL,'俄罗斯');

-- 创建city表
CREATE TABLE city(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 城市id
	city_name VARCHAR(30),			-- 城市名称
	cid INT, -- 外键列。关联country表的主键列id
	CONSTRAINT cc_fk1 FOREIGN KEY (cid) REFERENCES country(id)  -- 添加外键约束
);
-- 添加数据
INSERT INTO city VALUES (NULL,'北京',1),(NULL,'上海',1),(NULL,'纽约',2),(NULL,'莫斯科',3);

```

#### 创建视图

```
/*
	创建视图
	标准语法
		CREATE VIEW 视图名称 [(列名列表)] AS 查询语句;
*/
```



#### 查询视图
##### 查询视图

```
/*
	
	标准语法
		SELECT * FROM 视图名称;
*/
```
##### 查询创建视图的语句

```
/*	
	标准语法
		SHOW CREATE VIEW 视图名称;
*/
```



#### 修改视图

##### 	修改视图表数据

```
/*
	标准语法
		UPDATE 视图名称 SET 列名=值 WHERE 条件;
*/
```

##### 修改视图表结构

```
/*
	修改视图表结构
	标准语法
		ALTER VIEW 视图名称 [(列名列表)] AS 查询语句;
*/
```



#### 删除视图

```
/*
	删除视图
	标准语法
		DROP VIEW [IF EXISTS] 视图名称;
*/
```



### 02存储过程和函数

#### 数据准备

```
-- 创建db8数据库
CREATE DATABASE db8;

-- 使用db8数据库
USE db8;

-- 创建学生表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 学生id
	NAME VARCHAR(20),			-- 学生姓名
	age INT,				-- 学生年龄
	gender VARCHAR(5),			-- 学生性别
	score INT                               -- 学生成绩
);
-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23,'男',95),(NULL,'李四',24,'男',98),
(NULL,'王五',25,'女',100),(NULL,'赵六',26,'女',90);




-- 按照性别进行分组，查询每组学生的总成绩。按照总成绩的升序排序
SELECT gender,SUM(score) getSum FROM student GROUP BY gender ORDER BY getSum ASC;
```

#### 创建存储过程

```
/*
	

	-- 修改分隔符为$
	DELIMITER $
	
	-- 标准语法
	CREATE PROCEDURE 存储过程名称(参数...)
	BEGIN
		sql语句;
	END$
	
	-- 修改分隔符为分号
	DELIMITER ;

*/
```

-- 创建stu_group()存储过程，封装 分组查询总成绩，并按照总成绩升序排序的功能

```
DELIMITER $

CREATE PROCEDURE stu_group()
BEGIN
	SELECT gender,SUM(score) getSum FROM student GROUP BY gender ORDER BY getSum ASC;
END$

DELIMITER ;
```

#### 调用存储过程

```
/*
	调用存储过程
	标准语法
		CALL 存储过程名称(实际参数);
*/
```

-- 调用stu_group存储过程

```
CALL stu_group();
```

#### 查询数据库中所有的存储过程

```
/*
	查询数据库中所有的存储过程
	标准语法
		SELECT name FROM mysql.proc WHERE db='数据库名称';
*/
```

-- 查看db8数据库中所有的存储过程

```
SELECT NAME FROM mysql.proc WHERE db='db8';
```

#### 查看存储过程的状态信息

```
/*
	查看存储过程的状态信息
	标准语法
		SHOW PROCEDURE STATUS;
*/
```

-- 查看存储过程的状态信息

```
SHOW PROCEDURE STATUS;
```

#### 查看存储过程的创建语句

```
/*
	查看存储过程的创建语句
	标准语法
		SHOW CREATE PROCEDURE 存储过程名称;
*/
```

-- 查询stu_group存储过程的创建语句

```
SHOW CREATE PROCEDURE stu_group;
```

#### 删除存储过程

```
/*
	删除存储过程
	标准语法
		DROP PROCEDURE [IF EXISTS] 存储过程名称;
*/
```

-- 删除stu_group存储过程

```
DROP PROCEDURE IF EXISTS stu_group;
```

#### 存储过程-变量

##### 	定义变量

```
/*
	定义变量
	标准语法
		DECLARE 变量名 数据类型 [DEFAULT 默认值];
*/
```

-- 定义一个int类型变量，并赋默认值为10

```
DELIMITER $

CREATE PROCEDURE pro_test1()
BEGIN
	-- 定义变量
	DECLARE num INT DEFAULT 10;
	-- 查询变量
	SELECT num;
END$

DELIMITER ;
```

-- 调用pro_test1存储过程

```
CALL pro_test1();
```

##### 变量赋值-方式一

```
/*
	变量赋值-方式一
	标准语法
		SET 变量名 = 变量值;
*/
```

-- 定义一个varchar类型变量并赋值

```
DELIMITER $

CREATE PROCEDURE pro_test2()
BEGIN
	-- 定义变量
	DECLARE NAME VARCHAR(10);
	-- 为变量赋值
	SET NAME = '存储过程';
	-- 查询变量
	SELECT NAME;
END$

DELIMITER ;
```

-- 调用pro_test2存储过程

```
CALL pro_test2();
```



变量赋值-方式二

```
/*
	变量赋值-方式二
	标准语法
		SELECT 列名 INTO 变量名 FROM 表名 [WHERE 条件];
*/
```

-- 定义两个int变量，用于存储男女同学的总分数

```
DELIMITER $

CREATE PROCEDURE pro_test3()
BEGIN
	-- 定义变量
	DECLARE men,women INT;
	-- 查询男同学的总分数，为men变量赋值
	SELECT SUM(score) INTO men FROM student WHERE gender='男';
	-- 查询女同学的总分数，为women变量赋值
	SELECT SUM(score) INTO women FROM student WHERE gender='女';
	-- 查询变量
	SELECT men,women;
END$

DELIMITER ;
```

-- 调用pro_test3存储过程

```
CALL pro_test3();
```

#### 存储过程-if语句

```
/*
	存储过程-if语句
	标准语法
		IF 判断条件1 THEN 执行的sql语句1;
		[ELSEIF 判断条件2 THEN 执行的sql语句2;]
		...
		[ELSE 执行的sql语句n;]
		END IF;
*/
```

/*
	定义一个int变量，用于存储班级总成绩
	定义一个varchar变量，用于存储分数描述
	根据总成绩判断：
		380分及以上   学习优秀
		320 ~ 380     学习不错
		320以下       学习一般
*/

```
DELIMITER $

CREATE PROCEDURE pro_test4()
BEGIN
	-- 定义保存总成绩的变量
	DECLARE total INT;
	-- 定义保存分数描述的变量
	DECLARE description VARCHAR(10);
	-- 为total赋值
	SELECT SUM(score) INTO total FROM student;
	-- 判断总成绩
	IF total >= 380 THEN
		SET description = '学习优秀';
	ELSEIF total >= 320 AND total < 380 THEN
		SET description = '学习不错';
	ELSE
		SET description = '学习一般';
	END IF;
	

	-- 查询总成绩和描述信息
	SELECT total,description;

END$

DELIMITER ;
```

-- 调用pro_test4存储过程

```
CALL pro_test4();
```

#### 存储过程_输入参数

```
/*
	输入参数
	

	DELIMITER $
	
	-- 标准语法
	CREATE PROCEDURE 存储过程名称(IN 参数名 数据类型)
	BEGIN
		执行的sql语句;
	END$
	
	DELIMITER ;

*/
```

/*
	输入总成绩变量，代表学生总成绩
	定义一个varchar变量，用于存储分数描述
	根据总成绩判断：
		380分及以上  学习优秀
		320 ~ 380    学习不错
		320以下      学习一般
*/

```
DELIMITER $

CREATE PROCEDURE pro_test5(IN total INT)
BEGIN
	-- 定义分数描述变量
	DECLARE description VARCHAR(10);
	-- 判断总成绩
	IF total >= 380 THEN
		SET description = '学习优秀';
	ELSEIF total >= 320 AND total < 380 THEN
		SET description = '学习不错';
	ELSE
		SET description = '学习一般';
	END IF;
	

	-- 查询总成绩和描述信息
	SELECT total,description;

END$

DELIMITER ;
```

-- 调用pro_test5存储过程

```
CALL pro_test5(310);

CALL pro_test5((SELECT SUM(score) FROM student));
```

#### 存储过程_输出参数

```
/*
	输出参数
	

	DELIMITER $
	
	-- 标准语法
	CREATE PROCEDURE 存储过程名称(OUT 参数名 数据类型)
	BEGIN
		执行的sql语句;
	END$
	
	DELIMITER ;

*/
```

/输入总成绩变量，代表学生总成绩
	输出分数描述变量，代表学生总成绩的描述
	根据总成绩判断：
		380分及以上  学习优秀
		320 ~ 380    学习不错
		320以下      学习一般
*/

```
DELIMITER $

CREATE PROCEDURE pro_test6(IN total INT,OUT description VARCHAR(10))
BEGIN
	-- 判断总成绩
	IF total >= 380 THEN
		SET description = '学习优秀';
	ELSEIF total >= 320 AND total < 380 THEN
		SET description = '学习不错';
	ELSE
		SET description = '学习一般';
	END IF;
END$

DELIMITER ;
```

-- 调用pro_test6存储过程

```
CALL pro_test6(390,@description);
```

-- 查询总成绩描述信息变量

```
SELECT @description;
```

#### 存储过程_case语句

```
/*
	case语句
	标准语法
		CASE
		WHEN 判断条件1 THEN 执行sql语句1;
		[WHEN 判断条件2 THEN 执行sql语句2;]
		...
		[ELSE 执行sql语句n;]
		END CASE;
*/
```

/*
	输入总成绩变量，代表学生总成绩
	定义一个varchar变量，用于存储分数描述
	根据总成绩判断：
		380分及以上  学习优秀
		320 ~ 380    学习不错
		320以下      学习一般
*/

```
DELIMITER $

CREATE PROCEDURE pro_test7(IN total INT)
BEGIN
	-- 定义总成绩描述信息变量
	DECLARE description VARCHAR(10);
	-- 判断总成绩
	CASE
	WHEN total >= 380 THEN
		SET description = '学习优秀';
	WHEN total >= 320 AND total < 380 THEN
		SET description = '学习不错';
	ELSE
		SET description = '学习一般';
	END CASE;
	

	-- 查询总成绩和描述信息
	SELECT total,description;

END$

DELIMITER ;
```

-- 调用pro_test7存储过程

```
CALL pro_test7(310);
```

#### 存储过程_while循环

```
/*
	while循环
	标准语法
		初始化语句;
		WHILE 条件判断语句 DO
			循环体语句;
			条件控制语句;
		END WHILE;
*/
```

-- 计算1~100之间的偶数和

```
DELIMITER $

CREATE PROCEDURE pro_test8()
BEGIN
	-- 定义求和变量
	DECLARE result INT DEFAULT 0;
	-- 定义初始化变量
	DECLARE num INT DEFAULT 1;
	-- while循环
	WHILE num <= 100 DO
		-- 判断num是否是偶数
		IF num%2=0 THEN
			-- 让num和result进行累加
			SET result = result + num;
		END IF;
		

		-- 让num进行自增
		SET num = num + 1;
	END WHILE;
	
	-- 查询求和结果
	SELECT result;

END$

DELIMITER ;
```

-- 调用pro_test8存储过程

```
CALL pro_test8();
```

#### 存储过程_repeat循环

```
/*
	repeat循环
	标准语法
		初始化语句;
		REPEAT
			循环体语句;
			条件控制语句;
			UNTIL 条件判断语句
		END REPEAT;
*/
```

-- 计算1~10之间的和

```
DELIMITER $

CREATE PROCEDURE pro_test9()
BEGIN
	-- 定义求和变量
	DECLARE result INT DEFAULT 0;
	-- 定义初始化变量
	DECLARE num INT DEFAULT 1;
	-- repeat循环
	REPEAT
		-- 累加
		SET result = result + num;
		-- 让num+1
		SET num = num + 1;
		

		-- 停止循环
		UNTIL num > 10
	END REPEAT;
	
	-- 查询求和结果
	SELECT result;

END$

DELIMITER ;
```

-- 调用pro_test9存储过程

```
CALL pro_test9();
```

#### 存储过程_loop循环

```
/*
	loop循环
	标准语法
		初始化语句;
		[循环名称:] LOOP
			条件判断语句
				[LEAVE 循环名称;]
			循环体语句;
			条件控制语句;
		END LOOP 循环名称;
*/
```

-- 计算1~10之间的和

```
DELIMITER $

CREATE PROCEDURE pro_test10()
BEGIN
	-- 定义求和变量
	DECLARE result INT DEFAULT 0;
	-- 定义初始化变量
	DECLARE num INT DEFAULT 1;
	

	-- loop循环
	l:LOOP
		-- 条件判断，停止循环
		IF num > 10 THEN
			LEAVE l;
		END IF;
		
		-- 累加
		SET result = result + num;
		-- 让num+1
		SET num = num + 1;
	END LOOP l;
	
	-- 查询求和结果
	SELECT result;

END$

DELIMITER ;
```

-- 调用pro_test10存储过程

```
CALL pro_test10();
```

#### 存储过程_游标

```
/*
	游标
	标准语法
		1.创建游标：DECLARE 游标名称 CURSOR FOR 查询sql语句;
		2.打开游标：OPEN 游标名称;
		3.使用游标：FETCH 游标名称 INTO 变量名1,变量名2,...;
		4.关闭游标：CLOSE 游标名称;
*/
```

-- 创建stu_score表

```
CREATE TABLE stu_score(
	id INT PRIMARY KEY AUTO_INCREMENT,
	score INT
);
```



-- 将student表中所有的成绩保存到stu_score表中

```
DELIMITER $

CREATE PROCEDURE pro_test11()
BEGIN
	-- 定义一个保存成绩的变量
	DECLARE s_score INT;
	

	-- 创建游标
	DECLARE stu_result CURSOR FOR SELECT score FROM student;
	
	-- 开启游标
	OPEN stu_result;
	
	-- 使用游标
	-- 遍历结果，拿到第1行数据
	FETCH stu_result INTO s_score;
	-- 将数据保存到stu_score表中
	INSERT INTO stu_score VALUES (NULL,s_score);
	
	-- 遍历结果，拿到第2行数据
	FETCH stu_result INTO s_score;
	-- 将数据保存到stu_score表中
	INSERT INTO stu_score VALUES (NULL,s_score);
	
	-- 遍历结果，拿到第3行数据
	FETCH stu_result INTO s_score;
	-- 将数据保存到stu_score表中
	INSERT INTO stu_score VALUES (NULL,s_score);
	
	-- 遍历结果，拿到第4行数据
	FETCH stu_result INTO s_score;
	-- 将数据保存到stu_score表中
	INSERT INTO stu_score VALUES (NULL,s_score);
	
	-- 关闭游标
	CLOSE stu_result;

END$

DELIMITER ;
```

-- 调用pro_test11存储过程

```
CALL pro_test11();
```

-- 查询stu_score数据表

```
SELECT * FROM stu_score;
```



#### 存储过程_游标优化

```
/*
	当游标结束后，会自动的触发一个事件。就代表游标已经结束了！
	加标记的思想：
		1.定义一个标记变量，默认值为0（意味着有数据）
		2.当游标结束后，将标记变量的值修改为1（意味着没有数据了）	
*/
```

```
DELIMITER $

CREATE PROCEDURE pro_test12()
BEGIN
	-- 定义一个保存成绩的变量
	DECLARE s_score INT;
	

	-- 定义一个标记变量，默认值为0（意味着有数据）
	DECLARE flag INT DEFAULT 0;
	
	-- 创建游标
	DECLARE stu_result CURSOR FOR SELECT score FROM student;
	
	-- 当游标结束后，将标记变量的值修改为1（意味着没有数据了）
	DECLARE EXIT HANDLER FOR NOT FOUND SET flag = 1;
	
	-- 开启游标
	OPEN stu_result;
	
	-- 循环使用游标
	REPEAT
		-- 遍历结果，拿到数据
		FETCH stu_result INTO s_score;
		-- 将数据保存到stu_score表中
		INSERT INTO stu_score VALUES (NULL,s_score);
		
		-- 当flag的值是1的时候，就停止循环
		UNTIL flag=1
	END REPEAT;
	
	-- 关闭游标
	CLOSE stu_result;

END$

DELIMITER ;
```

-- 调用pro_test12存储过程

```
CALL pro_test12();
```

-- 查询stu_score表

```
SELECT * FROM stu_score;
```



#### 存储函数

##### 创建存储函数

```
/*
	创建存储函数
	标准语法
		CREATE FUNCTION 函数名称([参数 数据类型])
		RETURNS 返回值类型
		BEGIN
			执行的sql语句;
			RETURN 结果;
		END$
*/
```

-- 定义存储函数，获取学生表中成绩大于95分的学生数量

```
DELIMITER $

CREATE FUNCTION fun_test1()
RETURNS INT
BEGIN
	-- 定义一个统计变量
	DECLARE result INT;
	

	-- 查询成绩大于95分的数量
	SELECT COUNT(*) INTO result FROM student WHERE score > 95;
	
	-- 将结果返回
	RETURN result;

END$

DELIMITER ;
```



##### 调用函数

```
/*
	调用函数
	标准语法
		SELECT 函数名称(实际参数);
*/
```

-- 调用函数

```
SELECT fun_test1();
```

##### 删除函数

```
/*
	删除函数
	标准语法
		DROP FUNCTION 函数名称;
*/
```

-- 删除函数

```
DROP FUNCTION fun_test1;
```



### 03触发器

#### 触发器数据准备

```
-- 创建db9数据库
CREATE DATABASE db9;

-- 使用db9数据库
USE db9;

-- 创建账户表account
CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 账户id
	NAME VARCHAR(20),			-- 姓名
	money DOUBLE				-- 余额
);
-- 添加数据
INSERT INTO account VALUES (NULL,'张三',1000),(NULL,'李四',2000);


-- 创建日志表account_log
CREATE TABLE account_log(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 日志id
	operation VARCHAR(20),			-- 操作类型 (insert update delete)
	operation_time DATETIME,		-- 操作时间
	operation_id INT,			-- 操作表的id
	operation_params VARCHAR(200)       	-- 操作参数
);
```



#### INSERT型触发器

```
/*
	创建触发器
	标准语法
		DELIMITER $

		CREATE TRIGGER 触发器名称
		BEFORE|AFTER INSERT|UPDATE|DELETE
		ON 表名
		FOR EACH ROW
		BEGIN
			触发器要执行的功能;
		END$
	
		DELIMITER ;

*/
```

-- 创建INSERT型触发器。用于对account表新增数据进行日志的记录

```
DELIMITER $

CREATE TRIGGER account_insert
AFTER INSERT
ON account
FOR EACH ROW
BEGIN
	INSERT INTO account_log VALUES 
	(NULL,'INSERT',NOW(),new.id,CONCAT('插入后{id=',new.id,',name=',new.name,',money=',new.money,'}'));
END$

DELIMITER ;
```

-- 向account表来添加一条记录

```
INSERT INTO account VALUES (NULL,'王五',3000);
```

-- 查询account表

```
SELECT * FROM account;
```

-- 查询account_log表

```
SELECT * FROM account_log;
```



#### UPDATE型触发器

```
/*
	创建触发器
	标准语法
		DELIMITER $

		CREATE TRIGGER 触发器名称
		BEFORE|AFTER INSERT|UPDATE|DELETE
		ON 表名
		FOR EACH ROW
		BEGIN
			触发器要执行的功能;
		END$
	
		DELIMITER ;

*/
```

-- 创建UPDATE型触发器。用于对account表修改数据进行日志的记录

```
DELIMITER $

CREATE TRIGGER account_update
AFTER UPDATE
ON account
FOR EACH ROW
BEGIN
	INSERT INTO account_log VALUES 
	(NULL,'UPDATE',NOW(),new.id,CONCAT('修改前{id=',old.id,',name=',old.name,',money=',old.money,'}','修改后{id=',new.id,',name=',new.name,',money=',new.money,'}'));
END$

DELIMITER ;
```

-- 修改account表中李四的金额为2500

```
UPDATE account SET money=2500 WHERE NAME='李四';
```

-- 查询account表

```
SELECT * FROM account;
```

-- 查询account_log表

```
SELECT * FROM account_log;
```



#### DELETE型触发器

```
/*
	创建触发器
	标准语法
		DELIMITER $

		CREATE TRIGGER 触发器名称
		BEFORE|AFTER INSERT|UPDATE|DELETE
		ON 表名
		FOR EACH ROW
		BEGIN
			触发器要执行的功能;
		END$
	
		DELIMITER ;

*/
```

-- 创建DELETE型触发器。用于对account表删除数据进行日志的记录

```
DELIMITER $

CREATE TRIGGER account_delete
AFTER DELETE
ON account
FOR EACH ROW
BEGIN
	INSERT INTO account_log VALUES 
	(NULL,'DELETE',NOW(),old.id,CONCAT('删除前{id=',old.id,',name=',old.name,',money=',old.money,'}'));
END$

DELIMITER ;
```

-- 删除account表中王五

```
DELETE FROM account WHERE NAME='王五';
```

-- 查询account表

```
SELECT * FROM account;
```

-- 查询account_log表

```
SELECT * FROM account_log;
```



#### 查看触发器

```
/*
	查看触发器
	标准语法
		SHOW TRIGGERS;
*/
```

-- 查看触发器

```
SHOW TRIGGERS;
```



#### 删除触发器

```
/*
	删除触发器
	标准语法
		DROP TRIGGER 触发器名称;
*/
```

-- 删除触发器

```
DROP TRIGGER account_delete;
```



### 04事务

####  事务的数据准备

```
-- 创建db10数据库
CREATE DATABASE db10;

-- 使用db10数据库
USE db10;

-- 创建账户表
CREATE TABLE account(
	id INT PRIMARY KEY AUTO_INCREMENT,	-- 账户id
	NAME VARCHAR(20),			-- 账户名称
	money DOUBLE				-- 账户余额
);
-- 添加数据
INSERT INTO account VALUES (NULL,'张三',1000),(NULL,'李四',1000);
```



####  未管理事务

-- 张三给李四转账500元
-- 1.张三账户-500

```
UPDATE account SET money=money-500 WHERE NAME='张三';
```

出错了...

-- 2.李四账户+500

```
UPDATE account SET money=money+500 WHERE NAME='李四';
```



####  管理事务

```
/*
	开启事务：START TRANSACTION;
	回滚事务：ROLLBACK;
	提交事务：COMMIT;
*/
```

-- 张三给李四转账500元
-- 1.开启事务

```
START TRANSACTION;
```

-- 2.执行转账的操作
UPDATE account SET money=money-500 WHERE NAME='张三';
出错了...
UPDATE account SET money=money+500 WHERE NAME='李四';

-- 3.结束事务
-- 3.1回滚事务

```
ROLLBACK;
```

-- 3.2提交事务

```
COMMIT;
```



####  事务的提交方式

```
/*
	事务的提交方式
		查询事务提交方式：SELECT @@AUTOCOMMIT;  -- 1代表自动提交    0代表手动提交
		修改事务提交方式：SET @@AUTOCOMMIT=数字;
*/
```

-- 查询事务的提交方式

```
SELECT @@autocommit;

UPDATE account SET money=1000 WHERE NAME='张三';

COMMIT;
```

-- 修改事务的提交方式

```
SET @@autocommit=0;
```



####  查询和修改事务的隔离级别

```
/*
	事务的隔离级别
		查询隔离级别：SELECT @@TX_ISOLATION;
		修改隔离级别：SET GLOBAL TRANSACTION ISOLATION LEVEL 级别字符串;
*/
```

-- 查询事务隔离级别

```
SELECT @@tx_isolation;
```

-- 修改事务隔离级别

```
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
```



####  脏读的问题演示和解决_窗口1

```
/*
	脏读的问题演示和解决
	脏读：一个事务中读取到了其他事务未提交的数据
*/
```

-- 设置事务隔离级别为read uncommitted

```
SET GLOBAL TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
```

-- 开启事务

```
START TRANSACTION;
```

-- 转账

```
UPDATE account SET money = money-500 WHERE NAME='张三';
UPDATE account SET money = money+500 WHERE NAME='李四';
```

-- 查询account表

```
SELECT * FROM account;
```

-- 回滚

```
ROLLBACK;
```



####  脏读的问题演示和解决_窗口2

-- 查询事务隔离级别

```
SELECT @@tx_isolation;
```

-- 开启事务

```
START TRANSACTION;
```

-- 查询account表

```
SELECT * FROM account;
```



####  不可重复读的问题演示和解决_窗口1

```
/*
	不可重复读的问题演示和解决
	不可重复读：一个事务中读取到了其他事务已提交的数据
*/
```

-- 设置事务隔离级别为read committed

```
SET GLOBAL TRANSACTION ISOLATION LEVEL READ COMMITTED;
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
```

-- 开启事务

```
START TRANSACTION;
```

-- 转账

```
UPDATE account SET money = money-500 WHERE NAME='张三';
UPDATE account SET money = money+500 WHERE NAME='李四';
```

-- 查询account表

```
SELECT * FROM account;
```

-- 提交事务

```
COMMIT;
```



####  不可重复读的问题演示和解决_窗口2

-- 查询隔离级别

```
SELECT @@tx_isolation;
```

-- 开启事务

```
START TRANSACTION;
```

-- 查询account表

```
SELECT * FROM account;
```

-- 提交事务

```
COMMIT;
```



####  幻读的问题演示和解决_窗口1

```
/*
	幻读的问题演示和解决
	幻读：
	查询某记录是否存在，不存在
	准备插入此记录，但执行插入时发现此记录已存在，无法插入
	或某记录不存在执行删除，却发现删除成功
*/
```

-- 设置隔离级别为repeatable read

```
SET GLOBAL TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SET GLOBAL TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```

-- 开启事务

```
START TRANSACTION;
```

-- 添加记录

```
INSERT INTO account VALUES (4,'赵六',2000);
```

-- 查询account表

```
SELECT * FROM account;
```

-- 提交事务

```
COMMIT;
```



####  幻读的问题演示和解决_窗口2

-- 查询隔离级别

```
SELECT @@tx_isolation;
```

-- 开启事务

```
START TRANSACTION;
```

-- 查询account表

```
SELECT * FROM account;
```

-- 添加

```
INSERT INTO account VALUES (3,'王五',2000);
```



## day04

###  01存储引擎

#### 查询数据库支持的存储引擎

```
/*
	查询数据库支持的存储引擎
	标准语法
		SHOW ENGINES;
*/
```

#### 查询某个数据库中所有数据表的存储引擎

```
/*
	查询某个数据库中所有数据表的存储引擎
	标准语法
		SHOW TABLE STATUS FROM 数据库名称;
*/
```




#### 查询某个数据库中某个表的存储引擎

```
/*
	查询某个数据库中某个表的存储引擎
	标准语法
		SHOW TABLE STATUS FROM 数据库名称 WHERE NAME = '数据表名称';
*/
```

#### 创建数据表指定存储引擎

```
/*
	创建数据表指定存储引擎
	标准语法
		CREATE TABLE 表名(
			列名,数据类型,
			...
		)ENGINE = 引擎名称;
*/
```

#### 修改数据表的存储引擎

```
/*
	修改数据表的存储引擎
	标准语法
		ALTER TABLE 表名 ENGINE = 引擎名称;
*/
```

-- 修改engine_test表的引擎为InnoDB

```
ALTER TABLE engine_test ENGINE = INNODB;
```

-- 查询engine_test表的存储引擎

```
SHOW TABLE STATUS FROM db11 WHERE NAME ='engine_test';
```



###  02索引

#### 索引数据准备

```
-- 创建db12数据库
CREATE DATABASE db12;

-- 使用db12数据库
USE db12;

-- 创建student表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10),
	age INT,
	score INT
);
-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23,98),(NULL,'李四',24,95),
(NULL,'王五',25,96),(NULL,'赵六',26,94),(NULL,'周七',27,99);
```

#### 创建和查询索引

##### 创建索引

```
/*
	创建索引
	标准语法
		CREATE [UNIQUE|FULLTEXT] INDEX 索引名称
		[USING 索引类型]  -- 默认是B+TREE
		ON 表名(列名...);
*/
```

-- 为student表中的name列创建一个普通索引

```
CREATE INDEX idx_name ON student(NAME);
```

-- 为student表中的age列创建一个唯一索引

```
CREATE UNIQUE INDEX idx_age ON student(age);
```

##### 查询索引

```
/*
	查询索引
	标准语法
		SHOW INDEX FROM 表名;
*/
```

-- 查询student表中的索引

```
SHOW INDEX FROM student;
```

#### ALTER添加索引

```
/*
	ALTER添加索引
	-- 普通索引
	ALTER TABLE 表名 ADD INDEX 索引名称(列名);

    -- 组合索引
    ALTER TABLE 表名 ADD INDEX 索引名称(列名1,列名2,...);
    
    -- 主键索引
    ALTER TABLE 表名 ADD PRIMARY KEY(主键列名); 
    
    -- 外键索引(添加外键约束，就是外键索引)
    ALTER TABLE 表名 ADD CONSTRAINT 外键名 FOREIGN KEY (本表外键列名) REFERENCES 主表名(主键列名);
    
    -- 唯一索引
    ALTER TABLE 表名 ADD UNIQUE 索引名称(列名);
    
    -- 全文索引
    ALTER TABLE 表名 ADD FULLTEXT 索引名称(列名);

*/

```

-- 为student表中name列添加全文索引

```
ALTER TABLE student ADD FULLTEXT idx_fulltext_name(NAME);
```

-- 查询student表的索引

```
SHOW INDEX FROM student;
```

#### 删除索引

```
/*
	删除索引
	标准语法
		DROP INDEX 索引名称 ON 表名;
*/
```

-- 删除idx_name索引

```
DROP INDEX idx_name ON student;
```

#### 索引的效率测试

```
-- 创建product商品表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,  -- 商品id
	NAME VARCHAR(10),		    -- 商品名称
	price INT                           -- 商品价格
);

-- 定义存储函数，生成长度为10的随机字符串并返回
DELIMITER $

CREATE FUNCTION rand_string() 
RETURNS VARCHAR(255)
BEGIN
	DECLARE big_str VARCHAR(100) DEFAULT 'abcdefghijklmnopqrstuvwxyzABCDEFGHIGKLMNOPQRSTUVWXYZ';
	DECLARE small_str VARCHAR(255) DEFAULT '';
	DECLARE i INT DEFAULT 1;
	
	WHILE i <= 10 DO
		SET small_str =CONCAT(small_str,SUBSTRING(big_str,FLOOR(1+RAND()*52),1));
		SET i=i+1;
	END WHILE;
	
	RETURN small_str;
END$

DELIMITER ;



-- 定义存储过程，添加100万条数据到product表中
DELIMITER $

CREATE PROCEDURE pro_test()
BEGIN
	DECLARE num INT DEFAULT 1;
	
	WHILE num <= 1000000 DO
		INSERT INTO product VALUES (NULL,rand_string(),num);
		SET num = num + 1;
	END WHILE;
END$

DELIMITER ;

-- 调用存储过程
CALL pro_test();


-- 查询总记录条数
SELECT COUNT(*) FROM product;



-- 查询product表的索引
SHOW INDEX FROM product;

-- 查询name为OkIKDLVwtG的数据   (0.049)
SELECT * FROM product WHERE NAME='OkIKDLVwtG';

-- 通过id列查询OkIKDLVwtG的数据  (1毫秒)
SELECT * FROM product WHERE id=999998;

-- 为name列添加索引
ALTER TABLE product ADD INDEX idx_name(NAME);

-- 查询name为OkIKDLVwtG的数据   (0.001)
SELECT * FROM product WHERE NAME='OkIKDLVwtG';


/*
	范围查询
*/
-- 查询价格为800~1000之间的所有数据 (0.052)
SELECT * FROM product WHERE price BETWEEN 800 AND 1000;

/*
	排序查询
*/
-- 查询价格为800~1000之间的所有数据,降序排列  (0.083)
SELECT * FROM product WHERE price BETWEEN 800 AND 1000 ORDER BY price DESC;

-- 为price列添加索引
ALTER TABLE product ADD INDEX idx_price(price);

-- 查询价格为800~1000之间的所有数据 (0.011)
SELECT * FROM product WHERE price BETWEEN 800 AND 1000;

-- 查询价格为800~1000之间的所有数据,降序排列  (0.001)
SELECT * FROM product WHERE price BETWEEN 800 AND 1000 ORDER BY price DESC;
```



###  03锁

#### InnoDB锁的数据准备

```
-- 创建db13数据库
CREATE DATABASE db13;

-- 使用db13数据库
USE db13;

-- 创建student表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10),
	age INT,
	score INT
);
-- 添加数据
INSERT INTO student VALUES (NULL,'张三',23,99),(NULL,'李四',24,95),
(NULL,'王五',25,98),(NULL,'赵六',26,97);
```

#### InnoDB共享锁_窗口1

```
-- 窗口1
/*
	共享锁：数据可以被多个事务查询，但是不能修改
	创建锁的格式
		SELECT语句 LOCK IN SHARE MODE;
*/
-- 开启事务
START TRANSACTION;

-- 查询id为1的数据记录。并且加入共享锁
SELECT * FROM student WHERE id=1 LOCK IN SHARE MODE;

-- 查询分数为99的数据记录。并且加入共享锁
SELECT * FROM student WHERE score=99 LOCK IN SHARE MODE;

-- 提交事务
COMMIT;
```



#### InnoDB共享锁_窗口2

```
-- 开启事务
START TRANSACTION;

-- 查询id为1的数据记录。(普通查询没问题)
SELECT * FROM student WHERE id=1;

-- 查询id为1的数据记录。并且加入共享锁(共享锁和共享锁是兼容的)
SELECT * FROM student WHERE id=1 LOCK IN SHARE MODE;

-- 修改id为1的数据。修改姓名为张三三(修改失败，会出现锁的情况。只有窗口1提交事务后，才能修改成功)
UPDATE student SET NAME='张三三' WHERE id=1;

-- 修改id为2的数据。修改姓名为李四四(修改成功。InnoDB引擎默认是行锁)
UPDATE student SET NAME='李四四' WHERE id=2;

-- 修改id为3的数据。修改姓名为王五五(注意：InnoDB引擎如果不采用带索引的列，则会提升为表锁)
UPDATE student SET NAME='王五五' WHERE id=3;

-- 提交事务
COMMIT;
```



#### InnoDB排他锁_窗口1

```
-- 窗口1
/*
	排他锁：加锁的数据，不能被其他事务加锁查询或修改
	排他锁创建格式
		SELECT语句 FOR UPDATE;
*/
-- 开启事务
START TRANSACTION;

-- 查询id为1的数据记录。并且加入排他锁
SELECT * FROM student WHERE id=1 FOR UPDATE;

-- 提交事务
COMMIT;
```



#### InnoDB排他锁_窗口2

```
-- 开启事务
START TRANSACTION;

-- 查询id为1的数据记录。(普通查询没问题)
SELECT * FROM student WHERE id=1;

-- 查询id为1的数据记录。并且加入共享锁(排他锁不能和共享锁共存)
SELECT * FROM student WHERE id=1 LOCK IN SHARE MODE;

-- 查询id为1的数据记录。并且加入排他锁(排他锁和排他锁不能共存)
SELECT * FROM student WHERE id=1 FOR UPDATE;

-- 修改id为1的数据。将姓名修改为张三三(不能修改。会出现锁的情况。只有窗口1提交事务后，才能修改成功)
UPDATE student SET NAME='张三三' WHERE id=1;

-- 提交事务
COMMIT;
```



#### MYISAM锁的数据准备

```
-- 创建product表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20),
	price INT
)ENGINE = MYISAM;  -- 指定存储引擎为MyISAM

-- 添加数据
INSERT INTO product VALUES (NULL,'华为手机',4999),(NULL,'小米手机',2999),
(NULL,'苹果',8999),(NULL,'中兴',1999);
```



#### MYISAM读锁_窗口1

```
/*
	读锁：所有连接只能读取数据，不能修改
	加锁
		LOCK TABLE 表名 READ;

    解锁(将当前会话所有的表进行解锁)
    	UNLOCK TABLES;

*/

```

-- 为product表添加读锁

```
LOCK TABLE product READ;
```

-- 查询product表(查询成功)

```
SELECT * FROM product;
```

-- 将id为1的价格修改为5999(不能修改)

```
UPDATE product SET price=5999 WHERE id=1;
```

-- 解锁

```
UNLOCK TABLES;
```



#### MYISAM读锁_窗口2

```
-- 查询product表(查询成功)
SELECT * FROM product;

-- 将id为1的价格修改为5999(不能修改。只有窗口1解锁后才能修改成功)
UPDATE product SET price=5999 WHERE id=1;
```



#### MYISAM写锁_窗口1

```
/*
	写锁：其他连接不能查询和修改数据
	加锁
		LOCK TABLE 表名 WRITE;

	解锁(将当前会话所有的表进行解锁)
		UNLOCK TABLES;
*/
-- 为product表添加写锁
LOCK TABLE product WRITE;

-- 查询product表(查询成功)
SELECT * FROM product;

-- 修改id为2的价格为3999(修改成功)
UPDATE product SET price=3999 WHERE id=2;

-- 解锁
UNLOCK TABLES;
```



#### MYISAM写锁_窗口2

```
-- 查询product表（查询失败。只有窗口1解锁后，才能查询成功）
SELECT * FROM product;

-- 修改id为2的价格为1999(修改失败。只有窗口1解锁后，才能修改成功)
UPDATE product SET price=1999 WHERE id=2;
```



#### 乐观锁

```
-- 创建city表
CREATE TABLE city(
	id INT PRIMARY KEY AUTO_INCREMENT,  -- 城市id
	NAME VARCHAR(20),                   -- 城市名称
	VERSION INT                         -- 版本号
);

-- 添加数据
INSERT INTO city VALUES (NULL,'北京',1),(NULL,'上海',1),(NULL,'广州',1),(NULL,'深圳',1);


-- 将北京修改为北京市
-- 1.将北京的版本号读取出来
SELECT VERSION FROM city WHERE NAME='北京';   -- 1
-- 2.修改北京为北京市，版本号+1.并对比版本号是否相同
UPDATE city SET NAME='北京市',VERSION=VERSION+1 WHERE NAME='北京' AND VERSION=1;
```



###  04主从复制

#### 主从复制_主服务器操作

```
-- 主服务器创建db1数据库,从服务器会自动同步
CREATE DATABASE db1;
```



#### 主从复制_从服务器操作

```
-- 从服务器创建db2数据库,主服务器不会自动同步
CREATE DATABASE db2;
```



###  05读写分离

#### 读写分离_mycat操作

```
-- 创建学生表
CREATE TABLE student(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10)
);
-- 查询学生表
SELECT * FROM student;

-- 添加两条记录
INSERT INTO student VALUES (NULL,'张三'),(NULL,'李四');

-- 停止主从复制后，添加的数据只会保存到主服务器上。
INSERT INTO student VALUES (NULL,'王五');
```



#### 读写分离_主服务器操作

```
-- 主服务器：查询学生表，可以看到数据
SELECT * FROM student;
```



#### 读写分离_从服务器操作

```
-- 从服务器：查询学生表，可以看到数据(因为有主从复制)
SELECT * FROM student;

-- 从服务器：删除一条记录。(主服务器并没有删除，mycat中间件查询的结果是从服务器的数据)
DELETE FROM student WHERE id=2;
```



###  06水平拆分

#### 水平拆分_mycat操作

```
-- 创建product表
CREATE TABLE product(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(20),
	price INT
);

-- 添加6条数据
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'苹果手机',6999);
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'华为手机',5999); 
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'三星手机',4999); 
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'小米手机',3999); 
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'中兴手机',2999); 
INSERT INTO product(id,NAME,price) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'OOPO手机',1999); 

-- 查询product表
SELECT * FROM product; 
```



#### 水平拆分_主服务器操作

```
-- 在不同数据库中查询product表
SELECT * FROM product;
```



#### 水平拆分_从服务器操作

```
-- 在不同数据库中查询product表
SELECT * FROM product;
```



###  07垂直拆分

#### 垂直拆分_mycat操作

```
-- 创建dog表
CREATE TABLE dog(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10)
);
-- 添加数据
INSERT INTO dog(id,NAME) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'哈士奇');
-- 查询dog表
SELECT * FROM dog;


-- 创建cat表
CREATE TABLE cat(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10)
);
-- 添加数据
INSERT INTO cat(id,NAME) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'波斯猫');
-- 查询cat表
SELECT * FROM cat;





-- 创建apple表
CREATE TABLE apple(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10)
);
-- 添加数据
INSERT INTO apple(id,NAME) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'红富士');
-- 查询apple表
SELECT * FROM apple;


-- 创建banana表
CREATE TABLE banana(
	id INT PRIMARY KEY AUTO_INCREMENT,
	NAME VARCHAR(10)
);
-- 添加数据
INSERT INTO banana(id,NAME) VALUES (NEXT VALUE FOR MYCATSEQ_GLOBAL,'香蕉');
-- 查询banana表
SELECT * FROM banana;
```



#### 垂直拆分_主服务器操作

```
-- 查询dog表
SELECT * FROM dog;
-- 查询cat表
SELECT * FROM cat;


-- 查询apple表
SELECT * FROM apple;
-- 查询banana表
SELECT * FROM banana;
```



#### 垂直拆分_从服务器操作

```
-- 查询dog表
SELECT * FROM dog;
-- 查询cat表
SELECT * FROM cat;


-- 查询apple表
SELECT * FROM apple;
-- 查询banana表
SELECT * FROM banana;
```

