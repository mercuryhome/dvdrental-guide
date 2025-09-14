# PostgreSQL 17 - SQL 入门实验指导资料

## 项目简介

本项目是自学 PostgreSQL 17 SQL 时，为上机练习 DVD 租赁数据库案例准备的实验指导资料。

## 项目代码库
```
cd ~/workspace/database
git clone https://github.com/mercuryhome/dvdrental-guide.git
cd dvdrental-guide

~/workspace/database/dvdrental-guide
├── README.md                          # 项目说明文档
├── PostgreSQL17_SQL实验指导书.md        # 主要实验指导书
├── 数据库安装配置指南.md                 # 环境搭建指南
├── 实验练习答案.md                      # 练习答案参考
├── DVD租赁数据库详细结构说明.md          # 详细数据库结构说明
├── DVD租赁数据库设计过程.md              # 数据库设计过程文档
├── DVD租赁数据库表结构详细表格.md         # 表结构详细表格文档
├── dvdrental-ertool-export.sql         # ERD工具导出的SQL文件
├── dvdrental-erdtool.png              # ERD工具导出的关系图
└── 更新说明.md                        # 项目更新说明文档

```

## 学习路径

### 第一阶段：环境准备
1. 阅读 [数据库安装配置指南.md](./数据库安装配置指南.md)
2. 安装 PostgreSQL 17
3. 下载并恢复 DVD 租赁数据库
4. 配置数据库管理工具

### 第二阶段：基础学习
1. **实验一**：数据库环境搭建与基础查询
2. **实验二**：数据过滤与排序
3. **实验三**：多表连接查询
4. **实验四**：聚合函数与分组查询

### 第三阶段：进阶学习
5. **实验五**：子查询与复杂查询
6. **实验六**：数据修改操作
7. **实验七**：视图与索引
8. **实验八**：存储过程与函数

### 第四阶段：实践巩固
- 完成 [实验练习答案.md](./实验练习答案.md) 中的综合练习
- 尝试解决实际业务问题
- 学习性能优化技巧

### 补充学习资源
- 阅读 [DVD租赁数据库详细结构说明.md](./DVD租赁数据库详细结构说明.md) 了解完整的数据库结构
- 学习 [DVD租赁数据库设计过程.md](./DVD租赁数据库设计过程.md) 掌握基于王珊教授《数据库系统概论》第6版的完整设计方法论
- 查看 [DVD租赁数据库表结构详细表格.md](./DVD租赁数据库表结构详细表格.md) 获取所有表的详细字段定义和约束信息
- 查看 [DVD租赁数据库ER关系图](https://neon.com/_next/image?url=%2Fpostgresqltutorial%2Fdvd-rental-sample-database-diagram.png&w=640&q=75&dpl=dpl_EGWN7rHAPyoeARBxeB8mRi69u7b2) 理解表之间的关系
- 参考 [更新说明.md](./更新说明.md) 了解项目的最新更新内容

## 主要特性

### 📚 系统化学习
- 8个递进式实验，从基础到高级
- 每个实验目标明确，步骤清晰
- 包含详细的SQL注释和难点解释

### 🎯 实用性强
- 基于真实的DVD租赁业务场景
- 涵盖15个表，包含完整的业务逻辑
- 提供实际工作中常用的查询模式

### 🔧 技术先进
- 基于 PostgreSQL 17 最新版本
- 包含现代SQL特性（窗口函数、CTE等）
- 涵盖性能优化和最佳实践

### 📖 教学友好
- 中文注释和说明
- 详细的错误处理和问题解决
- 提供完整的练习答案
- 基于权威教材的设计方法论

## 数据库概览

详见 [数据库详细结构说明](DVD租赁数据库详细结构说明.md) 
![ER图](https://neon.com/_next/image?url=%2Fpostgresqltutorial%2Fdvd-rental-sample-database-diagram.png&w=640&q=75&dpl=dpl_EGWN7rHAPyoeARBxeB8mRi69u7b2)

## 快速开始

### 1. 环境要求
- PostgreSQL 17.x
- 数据库管理工具（pgAdmin 4 或 DBeaver）
- 文本编辑器

### 2. 安装步骤
```bash
# 1. 安装 PostgreSQL 17
# 参考：数据库安装配置指南.md

# 2. 下载数据库文件
wget https://www.postgresqltutorial.com/wp-content/uploads/2019/05/dvdrental.zip

# 3. 解压并恢复数据库
unzip dvdrental.zip
createdb -U postgres dvdrental
pg_restore -U postgres -d dvdrental dvdrental.tar

# 4. 验证安装
psql -U postgres -d dvdrental -c "SELECT COUNT(*) FROM film;"
```

### 3. 开始学习
1. 打开 [PostgreSQL17_SQL实验指导书.md](./PostgreSQL17_SQL实验指导书.md)
2. 按照实验顺序逐步学习
3. 完成每个实验的练习
4. 对照 [实验练习答案.md](./实验练习答案.md) 检查答案

## 学习建议

### 初学者
- 按顺序完成所有实验
- 重点掌握基础查询和连接操作
- 多练习，多思考

### 有经验者
- 可以直接跳转到感兴趣的实验
- 重点关注高级特性和性能优化
- 尝试解决复杂的业务问题

### 教师使用
- 可以作为数据库课程的实验教材
- 每个实验可以安排1-2个课时
- 建议结合理论讲解和实践操作

## 技术栈

- **数据库**: PostgreSQL 17
- **管理工具**: pgAdmin 4, DBeaver
- **示例数据**: DVD Rental Database
- **文档格式**: Markdown

## 贡献指南

欢迎提交问题报告和改进建议：

1. 发现错误或问题
2. 提出改进建议
3. 分享学习心得
4. 贡献新的实验内容

## 许可证

本项目采用 MIT 许可证，详见 LICENSE 文件。

## 相关资源

### 官方文档
- [PostgreSQL 17 官方文档](https://www.postgresql.org/docs/17/)
- [PostgreSQL 教程](https://www.postgresqltutorial.com/)

### 在线练习
- [PostgreSQL 练习平台](https://pgexercises.com/)
- [SQL 在线练习](https://www.w3schools.com/sql/)

### 学习社区
- [PostgreSQL 中文社区](https://www.postgresql.org/about/newsarchive/)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/postgresql)

## 更新日志

### v1.2.0 (2024-12-XX)
- 基于王珊教授《数据库系统概论》第6版优化了数据库设计过程文档
- 简化文档结构，提高可读性，从1669行减少到507行
- 遵循标准数据库设计方法论：需求分析→概念结构设计→逻辑结构设计→物理结构设计→数据库实施与维护
- 创建了专门的表结构详细表格文档
- 添加了ERD工具导出的SQL文件和关系图
- 优化了文档结构，避免内容重复
- 完善了数据类型、约束和索引的详细说明

### v1.1.0 (2024-12-XX)
- 创建了完整的数据库设计过程文档
- 添加了从业务需求到数据库实现的完整流程
- 包含了概念模型、ER模型、逻辑模型、物理模型设计
- 提供了基于实际SQL文件的修正说明

### v1.0.0 (2024-01-XX)
- 初始版本发布
- 包含8个完整实验
- 提供安装配置指南
- 包含练习答案

## 联系方式

如有问题或建议，请通过以下方式联系：

- 提交 Issue
- 发送邮件
- 参与讨论

---

**开始您的 PostgreSQL 学习之旅吧！** 🚀
