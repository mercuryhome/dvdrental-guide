# DVD 租赁数据库详细结构说明

## 概述

DVD 租赁数据库是一个完整的业务数据库，模拟了 DVD 租赁店的运营流程。该数据库包含 15 个表，涵盖了从电影管理、客户管理到租赁业务的全流程。

## 数据库ER设计及说明

![ER图](https://neon.com/_next/image?url=%2Fpostgresqltutorial%2Fdvd-rental-sample-database-diagram.png&w=640&q=75&dpl=dpl_EGWN7rHAPyoeARBxeB8mRi69u7b2)


根据 [Neon PostgreSQL 教程](https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database) 的说明，该数据库包含：

- **15 个表**：核心业务数据表
- **1 个触发器**：自动更新 last_update 字段
- **7 个视图**：提供便捷的数据查询接口
- **8 个函数**：封装常用业务逻辑
- **1 个域**：自定义数据类型
- **13 个序列**：主键自增序列

## 表结构详细说明

### 1. 电影相关表（5个表）

#### 1.1 film（电影基本信息表）
**用途**：存储电影的核心信息
**数据量**：1000+ 部电影

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| film_id | integer | 电影唯一标识 | 主键 |
| title | varchar(255) | 电影标题 | 非空 |
| description | text | 电影描述 | 可空 |
| release_year | integer | 发行年份 | 可空 |
| language_id | smallint | 语言ID | 外键→language |
| rental_duration | smallint | 租赁时长（天） | 非空，默认3 |
| rental_rate | numeric(4,2) | 租赁费率 | 非空，默认4.99 |
| length | smallint | 电影时长（分钟） | 可空 |
| replacement_cost | numeric(5,2) | 替换成本 | 非空，默认19.99 |
| rating | mpaa_rating | 电影评级 | 可空 |
| special_features | text[] | 特殊功能 | 可空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 支持多种电影评级（G, PG, PG-13, R, NC-17）
- special_features 字段存储数组，包含导演评论、删除场景等
- rental_duration 和 rental_rate 决定租赁政策

#### 1.2 language（语言信息表）
**用途**：存储电影语言信息
**数据量**：6 种语言

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| language_id | integer | 语言唯一标识 | 主键 |
| name | char(20) | 语言名称 | 非空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 支持多语言电影管理
- 主要语言包括英语、意大利语、日语、中文、法语、德语

#### 1.3 category（电影分类表）
**用途**：存储电影分类信息
**数据量**：16 个分类

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| category_id | integer | 分类唯一标识 | 主键 |
| name | varchar(25) | 分类名称 | 非空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 包含动作、喜剧、剧情、恐怖、科幻、动画等分类
- 支持电影的多分类管理

#### 1.4 film_category（电影与分类关联表）
**用途**：实现电影与分类的多对多关系
**数据量**：1000+ 条关联记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| film_id | smallint | 电影ID | 外键→film，主键 |
| category_id | smallint | 分类ID | 外键→category，主键 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 复合主键确保一部电影在一个分类中只出现一次
- 一部电影可以属于多个分类

#### 1.5 film_actor（电影与演员关联表）
**用途**：实现电影与演员的多对多关系
**数据量**：5462 条关联记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| actor_id | smallint | 演员ID | 外键→actor，主键 |
| film_id | smallint | 电影ID | 外键→film，主键 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 复合主键确保一个演员在一部电影中只出现一次
- 支持复杂的演员-电影关系查询

### 2. 人员相关表（3个表）

#### 2.1 actor（演员信息表）
**用途**：存储演员基本信息
**数据量**：200+ 名演员

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| actor_id | integer | 演员唯一标识 | 主键 |
| first_name | varchar(45) | 名字 | 非空 |
| last_name | varchar(45) | 姓氏 | 非空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 存储演员的基本身份信息
- 支持按姓名进行模糊查询

#### 2.2 customer（客户信息表）
**用途**：存储客户基本信息
**数据量**：600+ 名客户

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| customer_id | integer | 客户唯一标识 | 主键 |
| store_id | smallint | 所属店铺ID | 外键→store |
| first_name | varchar(45) | 名字 | 非空 |
| last_name | varchar(45) | 姓氏 | 非空 |
| email | varchar(50) | 邮箱地址 | 可空 |
| address_id | smallint | 地址ID | 外键→address |
| activebool | boolean | 是否活跃 | 非空，默认true |
| create_date | date | 创建日期 | 非空，默认当前日期 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |
| active | integer | 活跃状态 | 可空 |

**业务说明**：
- 客户与店铺关联，支持多店铺管理
- activebool 和 active 字段控制客户状态
- 支持客户历史记录查询

#### 2.3 staff（员工信息表）
**用途**：存储员工信息
**数据量**：2 名员工

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| staff_id | integer | 员工唯一标识 | 主键 |
| first_name | varchar(45) | 名字 | 非空 |
| last_name | varchar(45) | 姓氏 | 非空 |
| address_id | smallint | 地址ID | 外键→address |
| email | varchar(50) | 邮箱地址 | 可空 |
| store_id | smallint | 所属店铺ID | 外键→store |
| active | boolean | 是否活跃 | 非空，默认true |
| username | varchar(16) | 用户名 | 非空 |
| password | varchar(40) | 密码 | 可空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |
| picture | bytea | 头像图片 | 可空 |

**业务说明**：
- 员工可以管理多个店铺
- 包含登录凭据信息
- 支持员工权限管理

### 3. 业务相关表（4个表）

#### 3.1 store（店铺信息表）
**用途**：存储店铺信息
**数据量**：2 个店铺

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| store_id | integer | 店铺唯一标识 | 主键 |
| manager_staff_id | smallint | 经理员工ID | 外键→staff |
| address_id | smallint | 地址ID | 外键→address |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 每个店铺有一个经理
- 支持多店铺连锁经营模式

#### 3.2 inventory（库存信息表）
**用途**：存储电影库存信息
**数据量**：4581 条库存记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| inventory_id | integer | 库存唯一标识 | 主键 |
| film_id | smallint | 电影ID | 外键→film |
| store_id | smallint | 店铺ID | 外键→store |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 记录每部电影在各个店铺的库存数量
- 支持库存查询和统计

#### 3.3 rental（租赁记录表）
**用途**：存储租赁交易记录
**数据量**：16044 条租赁记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| rental_id | integer | 租赁唯一标识 | 主键 |
| rental_date | timestamp | 租赁日期 | 非空 |
| inventory_id | integer | 库存ID | 外键→inventory |
| customer_id | smallint | 客户ID | 外键→customer |
| return_date | timestamp | 归还日期 | 可空 |
| staff_id | smallint | 员工ID | 外键→staff |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 记录完整的租赁生命周期
- return_date 为空表示尚未归还
- 支持逾期查询和统计

#### 3.4 payment（支付记录表）
**用途**：存储支付交易记录
**数据量**：14596 条支付记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| payment_id | integer | 支付唯一标识 | 主键 |
| customer_id | smallint | 客户ID | 外键→customer |
| staff_id | smallint | 员工ID | 外键→staff |
| rental_id | integer | 租赁ID | 外键→rental |
| amount | numeric(5,2) | 支付金额 | 非空 |
| payment_date | timestamp | 支付日期 | 非空 |

**业务说明**：
- 记录所有支付交易
- 支持分期支付（一次租赁多次支付）
- 金额精确到分

### 4. 地址相关表（3个表）

#### 4.1 address（地址信息表）
**用途**：存储详细地址信息
**数据量**：603 条地址记录

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| address_id | integer | 地址唯一标识 | 主键 |
| address | varchar(50) | 街道地址 | 非空 |
| address2 | varchar(50) | 详细地址 | 可空 |
| district | varchar(20) | 区域 | 非空 |
| city_id | smallint | 城市ID | 外键→city |
| postal_code | varchar(10) | 邮政编码 | 可空 |
| phone | varchar(20) | 电话号码 | 非空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 支持国际地址格式
- 被客户、员工、店铺共享使用

#### 4.2 city（城市信息表）
**用途**：存储城市信息
**数据量**：600 个城市

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| city_id | integer | 城市唯一标识 | 主键 |
| city | varchar(50) | 城市名称 | 非空 |
| country_id | smallint | 国家ID | 外键→country |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 支持全球城市管理
- 与地址表形成层级关系

#### 4.3 country（国家信息表）
**用途**：存储国家信息
**数据量**：109 个国家

| 字段名 | 数据类型 | 说明 | 约束 |
|--------|----------|------|------|
| country_id | integer | 国家唯一标识 | 主键 |
| country | varchar(50) | 国家名称 | 非空 |
| last_update | timestamp | 最后更新时间 | 非空，默认当前时间 |

**业务说明**：
- 支持国际化业务
- 与城市表形成层级关系

## 表关系详细说明

### 1. 核心业务关系

#### 1.1 电影生态系统
```
film ←→ film_actor ←→ actor
  ↓
film_category ←→ category
  ↓
inventory ←→ store
```

**关系说明**：
- 电影与演员：多对多关系，通过 film_actor 表实现
- 电影与分类：多对多关系，通过 film_category 表实现
- 电影与库存：一对多关系，一部电影可以在多个店铺有库存

#### 1.2 租赁业务流程
```
customer → rental → inventory → film
    ↓         ↓
payment ← staff
```

**关系说明**：
- 客户与租赁：一对多关系，一个客户可以有多次租赁
- 租赁与库存：多对一关系，一次租赁对应一个库存项
- 租赁与支付：一对多关系，一次租赁可以有多次支付
- 员工参与租赁和支付流程

#### 1.3 地址层级关系
```
country → city → address → customer/staff/store
```

**关系说明**：
- 国家与城市：一对多关系
- 城市与地址：一对多关系
- 地址与人员/店铺：多对一关系

### 2. 外键约束关系

#### 2.1 主要外键关系
- `film.language_id` → `language.language_id`
- `film_category.film_id` → `film.film_id`
- `film_category.category_id` → `category.category_id`
- `film_actor.actor_id` → `actor.actor_id`
- `film_actor.film_id` → `film.film_id`
- `customer.store_id` → `store.store_id`
- `customer.address_id` → `address.address_id`
- `staff.address_id` → `address.address_id`
- `staff.store_id` → `store.store_id`
- `store.manager_staff_id` → `staff.staff_id`
- `store.address_id` → `address.address_id`
- `inventory.film_id` → `film.film_id`
- `inventory.store_id` → `store.store_id`
- `rental.inventory_id` → `inventory.inventory_id`
- `rental.customer_id` → `customer.customer_id`
- `rental.staff_id` → `staff.staff_id`
- `payment.customer_id` → `customer.customer_id`
- `payment.staff_id` → `staff.staff_id`
- `payment.rental_id` → `rental.rental_id`
- `address.city_id` → `city.city_id`
- `city.country_id` → `country.country_id`

### 3. 业务数据流

#### 3.1 租赁流程
1. **客户注册**：customer 表记录客户信息
2. **库存查询**：inventory 表查询可用电影
3. **创建租赁**：rental 表记录租赁信息
4. **处理支付**：payment 表记录支付信息
5. **归还处理**：更新 rental 表的 return_date

#### 3.2 数据统计流程
1. **电影统计**：通过 film、film_category、film_actor 表统计电影信息
2. **客户统计**：通过 customer、rental、payment 表统计客户行为
3. **库存统计**：通过 inventory、film 表统计库存情况
4. **收入统计**：通过 payment、rental 表统计收入情况

## 数据库设计特点

### 1. 规范化设计
- 符合第三范式，减少数据冗余
- 通过外键约束保证数据完整性
- 使用关联表实现多对多关系

### 2. 业务完整性
- 完整的租赁业务流程
- 支持多店铺管理
- 包含完整的地址体系

### 3. 扩展性设计
- 支持多语言电影
- 支持多分类管理
- 支持分期支付

### 4. 性能优化
- 主键自增设计
- 合理的索引设计
- 时间戳字段支持审计

## 学习价值

### 1. 关系型数据库设计
- 学习表关系设计
- 理解外键约束
- 掌握规范化理论

### 2. SQL 查询技能
- 复杂多表连接
- 聚合函数使用
- 子查询和窗口函数

### 3. 业务逻辑理解
- 理解租赁业务流程
- 学习数据建模方法
- 掌握业务分析技能

## 参考资源

- [Neon PostgreSQL 教程 - DVD 租赁数据库](https://neon.com/postgresql/postgresql-getting-started/postgresql-sample-database)
- [PostgreSQL 官方文档](https://www.postgresql.org/docs/)
- [数据库设计最佳实践](https://www.postgresqltutorial.com/)

---

*本文档基于 Neon PostgreSQL 教程中的 DVD 租赁数据库结构编写，用于学习和教学目的。*
