# PostgreSQL 17 SQL 实验指导书
## 基于 DVD 租赁数据库的学习实践

---

## 目录

1. [实验环境准备](#实验环境准备)
2. [实验一：数据库环境搭建与基础查询](#实验一数据库环境搭建与基础查询)
3. [实验二：数据过滤与排序](#实验二数据过滤与排序)
4. [实验三：多表连接查询](#实验三多表连接查询)
5. [实验四：聚合函数与分组查询](#实验四聚合函数与分组查询)
6. [实验五：子查询与复杂查询](#实验五子查询与复杂查询)
7. [实验六：数据修改操作](#实验六数据修改操作)
8. [实验七：视图与索引](#实验七视图与索引)
9. [实验八：存储过程与函数](#实验八存储过程与函数)
10. [附录：数据库结构说明](#附录数据库结构说明)

---

## 实验环境准备

### 1. 软件要求
- PostgreSQL 17.x
- pgAdmin 4 或 DBeaver（数据库管理工具）
- 文本编辑器（推荐 VS Code）

### 2. 数据库下载与安装
1. 下载 DVD 租赁示例数据库：https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database
2. 解压 `dvdrental.zip` 文件得到 `dvdrental.tar`
3. 使用 pgAdmin 或命令行工具恢复数据库

### 3. 数据库恢复命令
```bash
# 创建数据库
createdb dvdrental

# 恢复数据库
pg_restore -U postgres -d dvdrental dvdrental.tar
```

---

## 实验一：数据库环境搭建与基础查询

### 实验目标
- 熟悉 PostgreSQL 17 的基本操作
- 掌握基本的 SELECT 查询语句
- 了解 DVD 租赁数据库的基本结构

### 实验任务

#### 任务1：连接数据库并查看表结构
```sql
-- 连接到 dvdrental 数据库
\c dvdrental

-- 查看所有表
\dt

-- 查看特定表的结构（以 actor 表为例）
\d actor

-- 查看表的详细信息
SELECT column_name, data_type, is_nullable, column_default 
FROM information_schema.columns 
WHERE table_name = 'actor';
```

#### 任务2：基础查询练习
```sql
-- 1. 查询所有演员信息
SELECT * FROM actor;

-- 2. 查询演员的姓名（只显示前10条记录）
SELECT first_name, last_name 
FROM actor 
LIMIT 10;

-- 3. 查询所有电影标题
SELECT title 
FROM film 
LIMIT 5;

-- 4. 查询电影的基本信息
SELECT film_id, title, release_year, rating 
FROM film 
LIMIT 10;
```

#### 任务3：列别名和计算字段
```sql
-- 使用列别名
SELECT 
    first_name AS "名字",
    last_name AS "姓氏",
    first_name || ' ' || last_name AS "全名"
FROM actor 
LIMIT 5;

-- 计算字段示例
SELECT 
    title AS "电影标题",
    length AS "时长(分钟)",
    length / 60.0 AS "时长(小时)"
FROM film 
WHERE length IS NOT NULL
LIMIT 5;
```

### 难点解释
1. **字符串连接操作符 `||`**：PostgreSQL 使用双竖线进行字符串连接
2. **LIMIT 子句**：用于限制返回的记录数量
3. **列别名**：使用 AS 关键字或直接使用别名，中文别名需要用双引号

### 实验总结
- 掌握了基本的 SELECT 查询语法
- 学会了使用列别名和计算字段
- 了解了数据库的基本结构

### 深入学习建议
- 练习更多的字符串函数（SUBSTRING、UPPER、LOWER等）
- 了解 PostgreSQL 的数据类型系统
- 学习使用 `DISTINCT` 去除重复记录

---

## 实验二：数据过滤与排序

### 实验目标
- 掌握 WHERE 子句的使用
- 学会使用各种比较操作符
- 掌握 ORDER BY 排序功能
- 了解 LIKE 和 IN 操作符

### 实验任务

#### 任务1：基本条件查询
```sql
-- 1. 查询特定年份的电影
SELECT title, release_year, rating
FROM film
WHERE release_year = 2006;

-- 2. 查询时长超过120分钟的电影
SELECT title, length, rating
FROM film
WHERE length > 120
ORDER BY length DESC;

-- 3. 查询特定评级的电影
SELECT title, rating, rental_rate
FROM film
WHERE rating = 'PG-13';
```

#### 任务2：多条件查询
```sql
-- 使用 AND 操作符
SELECT title, release_year, length, rating
FROM film
WHERE release_year = 2006 AND length > 120;

-- 使用 OR 操作符
SELECT title, rating, rental_rate
FROM film
WHERE rating = 'PG' OR rating = 'PG-13';

-- 使用 IN 操作符（等价于多个 OR）
SELECT title, rating, rental_rate
FROM film
WHERE rating IN ('PG', 'PG-13', 'R');
```

#### 任务3：模糊查询和范围查询
```sql
-- 使用 LIKE 进行模糊查询
SELECT title, description
FROM film
WHERE title LIKE 'A%';  -- 以A开头的电影

SELECT title, description
FROM film
WHERE title LIKE '%Love%';  -- 包含Love的电影

-- 使用 BETWEEN 进行范围查询
SELECT title, length, rental_rate
FROM film
WHERE length BETWEEN 90 AND 120;

-- 使用 IS NULL 查询空值
SELECT title, special_features
FROM film
WHERE special_features IS NULL;
```

#### 任务4：排序查询
```sql
-- 单列排序
SELECT title, length, rating
FROM film
ORDER BY length DESC;  -- 按时长降序

-- 多列排序
SELECT title, length, rating, rental_rate
FROM film
ORDER BY rating ASC, length DESC;

-- 使用列位置排序
SELECT title, length, rating
FROM film
ORDER BY 2 DESC;  -- 按第2列（length）降序
```

### 难点解释
1. **LIKE 操作符**：
   - `%` 表示任意长度的字符串
   - `_` 表示单个字符
   - 区分大小写，可使用 `ILIKE` 进行不区分大小写的匹配

2. **NULL 值处理**：
   - 使用 `IS NULL` 和 `IS NOT NULL` 判断空值
   - 不能使用 `= NULL` 或 `!= NULL`

3. **BETWEEN 操作符**：
   - 包含边界值
   - 等价于 `column >= value1 AND column <= value2`

### 实验总结
- 掌握了各种条件查询方法
- 学会了使用排序功能
- 了解了模糊查询和范围查询

### 深入学习建议
- 学习正则表达式查询（`~` 操作符）
- 了解 `CASE` 条件表达式
- 练习复杂的多条件组合查询

---

## 实验三：多表连接查询

### 实验目标
- 理解表之间的关系
- 掌握 INNER JOIN 的使用
- 学会 LEFT JOIN 和 RIGHT JOIN
- 了解自连接和交叉连接

### 实验任务

#### 任务1：内连接（INNER JOIN）
```sql
-- 查询电影及其对应的语言
SELECT 
    f.title AS "电影标题",
    l.name AS "语言"
FROM film f
INNER JOIN language l ON f.language_id = l.language_id
LIMIT 10;

-- 查询演员及其参演的电影
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名",
    f.title AS "电影标题"
FROM actor a
INNER JOIN film_actor fa ON a.actor_id = fa.actor_id
INNER JOIN film f ON fa.film_id = f.film_id
LIMIT 10;
```

#### 任务2：左连接（LEFT JOIN）
```sql
-- 查询所有电影及其分类（包括没有分类的电影）
SELECT 
    f.title AS "电影标题",
    c.name AS "分类"
FROM film f
LEFT JOIN film_category fc ON f.film_id = fc.film_id
LEFT JOIN category c ON fc.category_id = c.category_id
LIMIT 15;

-- 查询所有客户及其租赁记录
SELECT 
    c.first_name || ' ' || c.last_name AS "客户姓名",
    r.rental_date AS "租赁日期"
FROM customer c
LEFT JOIN rental r ON c.customer_id = r.customer_id
LIMIT 10;
```

#### 任务3：多表连接查询
```sql
-- 查询租赁详细信息（客户、电影、员工）
SELECT 
    c.first_name || ' ' || c.last_name AS "客户姓名",
    f.title AS "电影标题",
    s.first_name || ' ' || s.last_name AS "员工姓名",
    r.rental_date AS "租赁日期",
    r.return_date AS "归还日期"
FROM rental r
INNER JOIN customer c ON r.customer_id = c.customer_id
INNER JOIN inventory i ON r.inventory_id = i.inventory_id
INNER JOIN film f ON i.film_id = f.film_id
INNER JOIN staff s ON r.staff_id = s.staff_id
LIMIT 10;
```

#### 任务4：自连接查询
```sql
-- 查询同一分类下的电影对
SELECT 
    f1.title AS "电影1",
    f2.title AS "电影2",
    c.name AS "共同分类"
FROM film f1
INNER JOIN film_category fc1 ON f1.film_id = fc1.film_id
INNER JOIN category c ON fc1.category_id = c.category_id
INNER JOIN film_category fc2 ON c.category_id = fc2.category_id
INNER JOIN film f2 ON fc2.film_id = f2.film_id
WHERE f1.film_id < f2.film_id  -- 避免重复和自匹配
LIMIT 10;
```

### 难点解释
1. **表别名**：使用别名简化查询，提高可读性
2. **连接条件**：确保连接条件正确，避免笛卡尔积
3. **连接类型选择**：
   - INNER JOIN：只返回两表都有匹配的记录
   - LEFT JOIN：返回左表所有记录，右表没有匹配的显示NULL
   - RIGHT JOIN：返回右表所有记录，左表没有匹配的显示NULL

### 实验总结
- 掌握了各种连接查询方法
- 理解了表之间的关系
- 学会了处理复杂的多表查询

### 深入学习建议
- 学习 FULL OUTER JOIN
- 了解连接性能优化
- 练习使用 EXISTS 和 NOT EXISTS

---

## 实验四：聚合函数与分组查询

### 实验目标
- 掌握常用聚合函数的使用
- 学会使用 GROUP BY 分组
- 理解 HAVING 子句的作用
- 了解窗口函数的基本概念

### 实验任务

#### 任务1：基本聚合函数
```sql
-- 统计电影总数
SELECT COUNT(*) AS "电影总数"
FROM film;

-- 计算电影的平均时长
SELECT 
    AVG(length) AS "平均时长",
    MIN(length) AS "最短时长",
    MAX(length) AS "最长时长"
FROM film
WHERE length IS NOT NULL;

-- 计算不同评级的电影数量
SELECT 
    rating AS "评级",
    COUNT(*) AS "电影数量"
FROM film
GROUP BY rating
ORDER BY COUNT(*) DESC;
```

#### 任务2：分组查询
```sql
-- 按分类统计电影数量和平均时长
SELECT 
    c.name AS "分类",
    COUNT(f.film_id) AS "电影数量",
    AVG(f.length) AS "平均时长",
    SUM(f.rental_rate) AS "总租金"
FROM category c
INNER JOIN film_category fc ON c.category_id = fc.category_id
INNER JOIN film f ON fc.film_id = f.film_id
GROUP BY c.category_id, c.name
ORDER BY COUNT(f.film_id) DESC;
```

#### 任务3：HAVING 子句
```sql
-- 查询电影数量超过10部的分类
SELECT 
    c.name AS "分类",
    COUNT(f.film_id) AS "电影数量"
FROM category c
INNER JOIN film_category fc ON c.category_id = fc.category_id
INNER JOIN film f ON fc.film_id = f.film_id
GROUP BY c.category_id, c.name
HAVING COUNT(f.film_id) > 10
ORDER BY COUNT(f.film_id) DESC;

-- 查询平均租金超过3.0的分类
SELECT 
    c.name AS "分类",
    AVG(f.rental_rate) AS "平均租金"
FROM category c
INNER JOIN film_category fc ON c.category_id = fc.category_id
INNER JOIN film f ON fc.film_id = f.film_id
GROUP BY c.category_id, c.name
HAVING AVG(f.rental_rate) > 3.0
ORDER BY AVG(f.rental_rate) DESC;
```

#### 任务4：客户消费统计
```sql
-- 统计每个客户的租赁次数和总消费
SELECT 
    c.first_name || ' ' || c.last_name AS "客户姓名",
    COUNT(r.rental_id) AS "租赁次数",
    SUM(p.amount) AS "总消费",
    AVG(p.amount) AS "平均消费"
FROM customer c
LEFT JOIN rental r ON c.customer_id = r.customer_id
LEFT JOIN payment p ON r.rental_id = p.rental_id
GROUP BY c.customer_id, c.first_name, c.last_name
HAVING COUNT(r.rental_id) > 0
ORDER BY SUM(p.amount) DESC
LIMIT 10;
```

### 难点解释
1. **GROUP BY 规则**：
   - SELECT 中的非聚合列必须出现在 GROUP BY 中
   - 可以使用列名、列位置或表达式

2. **HAVING vs WHERE**：
   - WHERE 在分组前过滤记录
   - HAVING 在分组后过滤组

3. **聚合函数处理 NULL**：
   - COUNT(*) 计算所有行
   - COUNT(column) 忽略 NULL 值
   - 其他聚合函数也忽略 NULL 值

### 实验总结
- 掌握了常用聚合函数的使用
- 学会了分组查询和条件过滤
- 理解了聚合查询的执行顺序

### 深入学习建议
- 学习 ROLLUP 和 CUBE 分组
- 了解窗口函数（ROW_NUMBER、RANK等）
- 练习复杂的统计分析查询

---

## 实验五：子查询与复杂查询

### 实验目标
- 掌握子查询的基本用法
- 学会使用相关子查询
- 了解 EXISTS 和 NOT EXISTS
- 掌握 UNION、INTERSECT、EXCEPT 操作

### 实验任务

#### 任务1：标量子查询
```sql
-- 查询高于平均租金的电影
SELECT title, rental_rate
FROM film
WHERE rental_rate > (
    SELECT AVG(rental_rate)
    FROM film
);

-- 查询每个分类中租金最高的电影
SELECT 
    f.title,
    f.rental_rate,
    c.name AS category_name
FROM film f
INNER JOIN film_category fc ON f.film_id = fc.film_id
INNER JOIN category c ON fc.category_id = c.category_id
WHERE f.rental_rate = (
    SELECT MAX(f2.rental_rate)
    FROM film f2
    INNER JOIN film_category fc2 ON f2.film_id = fc2.film_id
    WHERE fc2.category_id = fc.category_id
);
```

#### 任务2：IN 和 NOT IN 子查询
```sql
-- 查询参演过动作片的演员
SELECT DISTINCT
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE a.actor_id IN (
    SELECT fa.actor_id
    FROM film_actor fa
    INNER JOIN film_category fc ON fa.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = 'Action'
);

-- 查询从未租赁过电影的客户
SELECT 
    c.first_name || ' ' || c.last_name AS "客户姓名"
FROM customer c
WHERE c.customer_id NOT IN (
    SELECT DISTINCT customer_id
    FROM rental
    WHERE customer_id IS NOT NULL
);
```

#### 任务3：EXISTS 子查询
```sql
-- 查询有租赁记录的客户
SELECT 
    c.first_name || ' ' || c.last_name AS "客户姓名",
    c.email
FROM customer c
WHERE EXISTS (
    SELECT 1
    FROM rental r
    WHERE r.customer_id = c.customer_id
);

-- 查询参演电影数量超过10部的演员
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE EXISTS (
    SELECT 1
    FROM film_actor fa
    WHERE fa.actor_id = a.actor_id
    GROUP BY fa.actor_id
    HAVING COUNT(*) > 10
);
```

#### 任务4：集合操作
```sql
-- 查询同时参演动作片和喜剧片的演员
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE a.actor_id IN (
    SELECT fa.actor_id
    FROM film_actor fa
    INNER JOIN film_category fc ON fa.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = 'Action'
)
INTERSECT
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE a.actor_id IN (
    SELECT fa.actor_id
    FROM film_actor fa
    INNER JOIN film_category fc ON fa.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = 'Comedy'
);

-- 查询只参演动作片但未参演喜剧片的演员
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE a.actor_id IN (
    SELECT fa.actor_id
    FROM film_actor fa
    INNER JOIN film_category fc ON fa.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = 'Action'
)
EXCEPT
SELECT 
    a.first_name || ' ' || a.last_name AS "演员姓名"
FROM actor a
WHERE a.actor_id IN (
    SELECT fa.actor_id
    FROM film_actor fa
    INNER JOIN film_category fc ON fa.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = 'Comedy'
);
```

### 难点解释
1. **子查询类型**：
   - 标量子查询：返回单个值
   - 行子查询：返回单行多列
   - 表子查询：返回多行多列

2. **EXISTS vs IN**：
   - EXISTS 只关心是否存在匹配，不返回具体值
   - IN 需要返回具体的值进行比较
   - EXISTS 通常性能更好

3. **集合操作要求**：
   - 参与操作的两个查询必须有相同数量的列
   - 对应列的数据类型必须兼容

### 实验总结
- 掌握了各种子查询的使用方法
- 学会了使用集合操作
- 理解了复杂查询的逻辑结构

### 深入学习建议
- 学习 CTE（公用表表达式）
- 了解递归查询
- 练习性能优化技巧

---

## 实验六：数据修改操作

### 实验目标
- 掌握 INSERT 语句的使用
- 学会使用 UPDATE 修改数据
- 了解 DELETE 删除操作
- 掌握事务的基本概念

### 实验任务

#### 任务1：插入数据（INSERT）
```sql
-- 开始事务
BEGIN;

-- 插入新的语言记录
INSERT INTO language (name, last_update)
VALUES ('中文', CURRENT_TIMESTAMP);

-- 查看插入结果
SELECT * FROM language WHERE name = '中文';

-- 插入新的分类
INSERT INTO category (name, last_update)
VALUES ('科幻', CURRENT_TIMESTAMP);

-- 查看插入结果
SELECT * FROM category WHERE name = '科幻';

-- 提交事务
COMMIT;
```

#### 任务2：更新数据（UPDATE）
```sql
-- 开始事务
BEGIN;

-- 更新电影的租金（将PG-13电影的租金提高10%）
UPDATE film
SET rental_rate = rental_rate * 1.1,
    last_update = CURRENT_TIMESTAMP
WHERE rating = 'PG-13';

-- 查看更新结果
SELECT title, rating, rental_rate
FROM film
WHERE rating = 'PG-13'
LIMIT 5;

-- 更新客户信息
UPDATE customer
SET email = LOWER(email),
    last_update = CURRENT_TIMESTAMP
WHERE email IS NOT NULL;

-- 提交事务
COMMIT;
```

#### 任务3：删除数据（DELETE）
```sql
-- 开始事务
BEGIN;

-- 删除测试数据（删除我们之前插入的中文语言记录）
DELETE FROM language
WHERE name = '中文';

-- 删除测试数据（删除我们之前插入的科幻分类）
DELETE FROM category
WHERE name = '科幻';

-- 查看删除结果
SELECT * FROM language WHERE name = '中文';
SELECT * FROM category WHERE name = '科幻';

-- 提交事务
COMMIT;
```

#### 任务4：批量操作
```sql
-- 开始事务
BEGIN;

-- 批量插入新客户
INSERT INTO customer (store_id, first_name, last_name, email, address_id, activebool, create_date, last_update, active)
VALUES 
    (1, '张', '三', 'zhangsan@email.com', 1, true, CURRENT_DATE, CURRENT_TIMESTAMP, 1),
    (1, '李', '四', 'lisi@email.com', 2, true, CURRENT_DATE, CURRENT_TIMESTAMP, 1),
    (2, '王', '五', 'wangwu@email.com', 3, true, CURRENT_DATE, CURRENT_TIMESTAMP, 1);

-- 查看插入结果
SELECT customer_id, first_name, last_name, email
FROM customer
WHERE first_name IN ('张', '李', '王');

-- 批量更新（将新插入客户的邮箱转换为大写）
UPDATE customer
SET email = UPPER(email),
    last_update = CURRENT_TIMESTAMP
WHERE first_name IN ('张', '李', '王');

-- 查看更新结果
SELECT customer_id, first_name, last_name, email
FROM customer
WHERE first_name IN ('张', '李', '王');

-- 回滚事务（撤销所有更改）
ROLLBACK;
```

### 难点解释
1. **事务控制**：
   - BEGIN：开始事务
   - COMMIT：提交事务，永久保存更改
   - ROLLBACK：回滚事务，撤销所有更改

2. **UPDATE 注意事项**：
   - 使用 WHERE 子句限制更新范围
   - 可以同时更新多个列
   - 使用 CURRENT_TIMESTAMP 更新修改时间

3. **DELETE 注意事项**：
   - 使用 WHERE 子句避免误删
   - 考虑外键约束的影响
   - 删除前先备份重要数据

### 实验总结
- 掌握了数据修改的基本操作
- 学会了使用事务控制
- 了解了数据完整性的重要性

### 深入学习建议
- 学习 UPSERT 操作（ON CONFLICT）
- 了解 MERGE 语句
- 练习复杂的数据迁移操作

---

## 实验七：视图与索引

### 实验目标
- 理解视图的概念和作用
- 掌握创建和使用视图
- 了解索引的基本概念
- 学会创建和管理索引

### 实验任务

#### 任务1：创建和使用视图
```sql
-- 创建电影详细信息视图
CREATE OR REPLACE VIEW film_details AS
SELECT 
    f.film_id,
    f.title,
    f.description,
    f.release_year,
    f.length,
    f.rating,
    f.rental_rate,
    l.name AS language_name,
    c.name AS category_name
FROM film f
LEFT JOIN language l ON f.language_id = l.language_id
LEFT JOIN film_category fc ON f.film_id = fc.film_id
LEFT JOIN category c ON fc.category_id = c.category_id;

-- 使用视图查询数据
SELECT title, language_name, category_name, rating
FROM film_details
WHERE rating = 'PG-13'
LIMIT 10;

-- 创建客户租赁统计视图
CREATE OR REPLACE VIEW customer_rental_stats AS
SELECT 
    c.customer_id,
    c.first_name || ' ' || c.last_name AS customer_name,
    c.email,
    COUNT(r.rental_id) AS rental_count,
    SUM(p.amount) AS total_payment,
    AVG(p.amount) AS avg_payment
FROM customer c
LEFT JOIN rental r ON c.customer_id = r.customer_id
LEFT JOIN payment p ON r.rental_id = p.rental_id
GROUP BY c.customer_id, c.first_name, c.last_name, c.email;

-- 使用视图查询高消费客户
SELECT customer_name, rental_count, total_payment
FROM customer_rental_stats
WHERE total_payment > 100
ORDER BY total_payment DESC
LIMIT 10;
```

#### 任务2：可更新视图
```sql
-- 创建简单的可更新视图
CREATE OR REPLACE VIEW film_basic_info AS
SELECT film_id, title, rating, rental_rate
FROM film;

-- 通过视图更新数据
UPDATE film_basic_info
SET rental_rate = rental_rate * 1.05
WHERE rating = 'R';

-- 查看更新结果
SELECT title, rating, rental_rate
FROM film_basic_info
WHERE rating = 'R'
LIMIT 5;

-- 通过视图插入数据（需要满足视图的约束）
-- 注意：这需要film表的其他必填字段有默认值
```

#### 任务3：创建索引
```sql
-- 查看表的索引信息
SELECT 
    indexname,
    indexdef
FROM pg_indexes
WHERE tablename = 'film';

-- 为电影标题创建索引
CREATE INDEX idx_film_title ON film(title);

-- 为电影评级和租金创建复合索引
CREATE INDEX idx_film_rating_rental ON film(rating, rental_rate);

-- 为演员姓名创建索引
CREATE INDEX idx_actor_name ON actor(first_name, last_name);

-- 为租赁日期创建索引
CREATE INDEX idx_rental_date ON rental(rental_date);

-- 查看新创建的索引
SELECT 
    indexname,
    indexdef
FROM pg_indexes
WHERE tablename IN ('film', 'actor', 'rental')
ORDER BY tablename, indexname;
```

#### 任务4：索引性能测试
```sql
-- 测试查询性能（使用 EXPLAIN ANALYZE）
EXPLAIN ANALYZE
SELECT title, rating, rental_rate
FROM film
WHERE title LIKE 'A%';

-- 测试复合索引的性能
EXPLAIN ANALYZE
SELECT title, rating, rental_rate
FROM film
WHERE rating = 'PG-13' AND rental_rate > 3.0;

-- 测试演员查询性能
EXPLAIN ANALYZE
SELECT first_name, last_name
FROM actor
WHERE first_name = 'John';
```

### 难点解释
1. **视图类型**：
   - 简单视图：基于单个表，通常可更新
   - 复杂视图：包含连接、聚合等，通常只读

2. **索引选择**：
   - 主键和唯一约束自动创建索引
   - 为经常查询的列创建索引
   - 复合索引的顺序很重要

3. **性能分析**：
   - 使用 EXPLAIN ANALYZE 查看执行计划
   - 关注 Seq Scan（顺序扫描）vs Index Scan（索引扫描）

### 实验总结
- 掌握了视图的创建和使用
- 学会了创建和管理索引
- 了解了查询性能优化

### 深入学习建议
- 学习物化视图
- 了解索引类型（B-tree、Hash、GiST等）
- 练习查询优化技巧

---

## 实验八：存储过程与函数

### 实验目标
- 理解存储过程和函数的概念
- 掌握创建和使用存储过程
- 学会创建自定义函数
- 了解触发器的基本用法

### 实验任务

#### 任务1：创建简单函数
```sql
-- 创建计算电影总价值的函数
CREATE OR REPLACE FUNCTION calculate_film_value(
    p_rental_rate NUMERIC,
    p_replacement_cost NUMERIC
) RETURNS NUMERIC AS $$
BEGIN
    RETURN p_rental_rate + p_replacement_cost;
END;
$$ LANGUAGE plpgsql;

-- 使用函数
SELECT 
    title,
    rental_rate,
    replacement_cost,
    calculate_film_value(rental_rate, replacement_cost) AS total_value
FROM film
LIMIT 10;

-- 创建获取客户全名的函数
CREATE OR REPLACE FUNCTION get_customer_full_name(
    p_customer_id INTEGER
) RETURNS TEXT AS $$
DECLARE
    v_full_name TEXT;
BEGIN
    SELECT first_name || ' ' || last_name
    INTO v_full_name
    FROM customer
    WHERE customer_id = p_customer_id;
    
    RETURN v_full_name;
END;
$$ LANGUAGE plpgsql;

-- 使用函数
SELECT 
    customer_id,
    get_customer_full_name(customer_id) AS full_name
FROM customer
LIMIT 10;
```

#### 任务2：创建带参数的存储过程
```sql
-- 创建更新电影租金的存储过程
CREATE OR REPLACE FUNCTION update_film_rental_rate(
    p_film_id INTEGER,
    p_rate_increase NUMERIC
) RETURNS VOID AS $$
BEGIN
    UPDATE film
    SET rental_rate = rental_rate * (1 + p_rate_increase),
        last_update = CURRENT_TIMESTAMP
    WHERE film_id = p_film_id;
    
    -- 检查是否更新成功
    IF NOT FOUND THEN
        RAISE EXCEPTION 'Film with ID % not found', p_film_id;
    END IF;
END;
$$ LANGUAGE plpgsql;

-- 使用存储过程
SELECT update_film_rental_rate(1, 0.1);  -- 将电影ID为1的租金提高10%

-- 查看更新结果
SELECT film_id, title, rental_rate
FROM film
WHERE film_id = 1;
```

#### 任务3：创建返回表的函数
```sql
-- 创建获取指定分类电影的存储过程
CREATE OR REPLACE FUNCTION get_films_by_category(
    p_category_name TEXT
) RETURNS TABLE(
    film_id INTEGER,
    title TEXT,
    rating TEXT,
    rental_rate NUMERIC
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        f.film_id,
        f.title,
        f.rating,
        f.rental_rate
    FROM film f
    INNER JOIN film_category fc ON f.film_id = fc.film_id
    INNER JOIN category c ON fc.category_id = c.category_id
    WHERE c.name = p_category_name;
END;
$$ LANGUAGE plpgsql;

-- 使用函数
SELECT * FROM get_films_by_category('Action')
LIMIT 10;

-- 创建获取客户租赁统计的函数
CREATE OR REPLACE FUNCTION get_customer_rental_summary(
    p_customer_id INTEGER
) RETURNS TABLE(
    customer_name TEXT,
    total_rentals BIGINT,
    total_payment NUMERIC,
    avg_payment NUMERIC
) AS $$
BEGIN
    RETURN QUERY
    SELECT 
        c.first_name || ' ' || c.last_name,
        COUNT(r.rental_id),
        COALESCE(SUM(p.amount), 0),
        COALESCE(AVG(p.amount), 0)
    FROM customer c
    LEFT JOIN rental r ON c.customer_id = r.customer_id
    LEFT JOIN payment p ON r.rental_id = p.rental_id
    WHERE c.customer_id = p_customer_id
    GROUP BY c.customer_id, c.first_name, c.last_name;
END;
$$ LANGUAGE plpgsql;

-- 使用函数
SELECT * FROM get_customer_rental_summary(1);
```

#### 任务4：创建触发器
```sql
-- 创建更新last_update字段的触发器函数
CREATE OR REPLACE FUNCTION update_last_update_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.last_update = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 为film表创建触发器
CREATE TRIGGER trigger_film_last_update
    BEFORE UPDATE ON film
    FOR EACH ROW
    EXECUTE FUNCTION update_last_update_column();

-- 测试触发器
UPDATE film
SET rental_rate = rental_rate * 1.01
WHERE film_id = 1;

-- 查看触发器效果
SELECT film_id, title, rental_rate, last_update
FROM film
WHERE film_id = 1;
```

### 难点解释
1. **函数 vs 存储过程**：
   - 函数必须返回值，存储过程可以不返回值
   - 函数可以在SELECT中使用，存储过程用CALL调用

2. **PL/pgSQL 语法**：
   - 使用 `$$` 作为函数体分隔符
   - DECLARE 部分声明变量
   - BEGIN...END 构成代码块

3. **触发器**：
   - BEFORE：在操作前执行
   - AFTER：在操作后执行
   - NEW 和 OLD 记录分别表示新值和旧值

### 实验总结
- 掌握了存储过程和函数的创建
- 学会了使用 PL/pgSQL 语言
- 了解了触发器的基本用法

### 深入学习建议
- 学习异常处理（EXCEPTION）
- 了解游标的使用
- 练习复杂的业务逻辑实现

---

## 附录：数据库结构说明

### 数据库概览

DVD 租赁数据库是一个完整的业务数据库，包含 **15 个表**，模拟了 DVD 租赁店的完整运营流程。该数据库基于 [Neon PostgreSQL 教程](https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database) 提供的标准示例数据库。

### 数据库对象统计
- **15 个表**：核心业务数据表
- **1 个触发器**：自动更新 last_update 字段
- **7 个视图**：提供便捷的数据查询接口
- **8 个函数**：封装常用业务逻辑
- **1 个域**：自定义数据类型（mpaa_rating）
- **13 个序列**：主键自增序列

### 详细表结构

#### 1. 电影相关表（5个表）

**film（电影基本信息表）**
- 数据量：1000+ 部电影
- 主要字段：film_id, title, description, release_year, language_id, rental_duration, rental_rate, length, replacement_cost, rating, special_features
- 业务功能：存储电影的核心信息，包括标题、描述、发行年份、语言、租赁政策、评级等

**language（语言信息表）**
- 数据量：6 种语言
- 主要字段：language_id, name
- 业务功能：支持多语言电影管理

**category（电影分类表）**
- 数据量：16 个分类
- 主要字段：category_id, name
- 业务功能：包含动作、喜剧、剧情、恐怖、科幻、动画等分类

**film_category（电影与分类关联表）**
- 数据量：1000+ 条关联记录
- 主要字段：film_id, category_id
- 业务功能：实现电影与分类的多对多关系

**film_actor（电影与演员关联表）**
- 数据量：5462 条关联记录
- 主要字段：actor_id, film_id
- 业务功能：实现电影与演员的多对多关系

#### 2. 人员相关表（3个表）

**actor（演员信息表）**
- 数据量：200+ 名演员
- 主要字段：actor_id, first_name, last_name
- 业务功能：存储演员的基本身份信息

**customer（客户信息表）**
- 数据量：600+ 名客户
- 主要字段：customer_id, store_id, first_name, last_name, email, address_id, activebool, create_date, active
- 业务功能：记录客户基本信息、所属店铺、地址、活跃状态等

**staff（员工信息表）**
- 数据量：2 名员工
- 主要字段：staff_id, first_name, last_name, address_id, email, store_id, active, username, password, picture
- 业务功能：记录员工基本信息、所属店铺、登录凭据等

#### 3. 业务相关表（4个表）

**store（店铺信息表）**
- 数据量：2 个店铺
- 主要字段：store_id, manager_staff_id, address_id
- 业务功能：记录店铺位置、经理信息，支持多店铺连锁经营

**inventory（库存信息表）**
- 数据量：4581 条库存记录
- 主要字段：inventory_id, film_id, store_id
- 业务功能：记录每部电影在各个店铺的库存数量

**rental（租赁记录表）**
- 数据量：16044 条租赁记录
- 主要字段：rental_id, rental_date, inventory_id, customer_id, return_date, staff_id
- 业务功能：记录完整的租赁生命周期，包括租赁时间、归还时间、客户、员工等信息

**payment（支付记录表）**
- 数据量：14596 条支付记录
- 主要字段：payment_id, customer_id, staff_id, rental_id, amount, payment_date
- 业务功能：记录所有支付交易，支持分期支付

#### 4. 地址相关表（3个表）

**address（地址信息表）**
- 数据量：603 条地址记录
- 主要字段：address_id, address, address2, district, city_id, postal_code, phone
- 业务功能：存储详细地址信息，被客户、员工、店铺共享使用

**city（城市信息表）**
- 数据量：600 个城市
- 主要字段：city_id, city, country_id
- 业务功能：支持全球城市管理

**country（国家信息表）**
- 数据量：109 个国家
- 主要字段：country_id, country
- 业务功能：支持国际化业务

### 核心业务关系

#### 1. 电影生态系统
```
film ←→ film_actor ←→ actor
  ↓
film_category ←→ category
  ↓
inventory ←→ store
```

#### 2. 租赁业务流程
```
customer → rental → inventory → film
    ↓         ↓
payment ← staff
```

#### 3. 地址层级关系
```
country → city → address → customer/staff/store
```

### 主要外键关系
- 电影与语言：`film.language_id` → `language.language_id`
- 电影与分类：`film_category.film_id` → `film.film_id`
- 电影与演员：`film_actor.actor_id` → `actor.actor_id`
- 客户与店铺：`customer.store_id` → `store.store_id`
- 客户与地址：`customer.address_id` → `address.address_id`
- 员工与店铺：`staff.store_id` → `store.store_id`
- 店铺与经理：`store.manager_staff_id` → `staff.staff_id`
- 库存与电影：`inventory.film_id` → `film.film_id`
- 库存与店铺：`inventory.store_id` → `store.store_id`
- 租赁与库存：`rental.inventory_id` → `inventory.inventory_id`
- 租赁与客户：`rental.customer_id` → `customer.customer_id`
- 租赁与员工：`rental.staff_id` → `staff.staff_id`
- 支付与客户：`payment.customer_id` → `customer.customer_id`
- 支付与员工：`payment.staff_id` → `staff.staff_id`
- 支付与租赁：`payment.rental_id` → `rental.rental_id`
- 地址与城市：`address.city_id` → `city.city_id`
- 城市与国家：`city.country_id` → `country.country_id`

### 业务数据流

#### 租赁流程
1. **客户注册**：customer 表记录客户信息
2. **库存查询**：inventory 表查询可用电影
3. **创建租赁**：rental 表记录租赁信息
4. **处理支付**：payment 表记录支付信息
5. **归还处理**：更新 rental 表的 return_date

#### 数据统计流程
1. **电影统计**：通过 film、film_category、film_actor 表统计电影信息
2. **客户统计**：通过 customer、rental、payment 表统计客户行为
3. **库存统计**：通过 inventory、film 表统计库存情况
4. **收入统计**：通过 payment、rental 表统计收入情况

### 数据库设计特点

1. **规范化设计**：符合第三范式，减少数据冗余
2. **业务完整性**：完整的租赁业务流程，支持多店铺管理
3. **扩展性设计**：支持多语言电影、多分类管理、分期支付
4. **性能优化**：主键自增设计、合理的索引设计、时间戳字段支持审计

### 学习价值

1. **关系型数据库设计**：学习表关系设计、理解外键约束、掌握规范化理论
2. **SQL 查询技能**：复杂多表连接、聚合函数使用、子查询和窗口函数
3. **业务逻辑理解**：理解租赁业务流程、学习数据建模方法、掌握业务分析技能

### 参考资源

- [Neon PostgreSQL 教程 - DVD 租赁数据库](https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database)
- [PostgreSQL 官方文档](https://www.postgresql.org/docs/)
- [数据库设计最佳实践](https://www.postgresqltutorial.com/)

> **注意**：更详细的数据库结构说明请参考项目根目录下的 `DVD租赁数据库详细结构说明.md` 文件。

---

## 学习建议

### 循序渐进
1. 从基础查询开始，逐步掌握复杂查询
2. 重视实践，多写SQL语句
3. 理解数据库设计原理

### 扩展学习
1. 学习数据库设计理论
2. 了解性能优化技巧
3. 掌握数据库管理技能

### 实际应用
1. 尝试设计自己的数据库
2. 参与开源项目
3. 关注数据库技术发展

---

*本实验指导书基于PostgreSQL 17和DVD租赁数据库设计，适合数据库初学者和进阶学习者使用。*
