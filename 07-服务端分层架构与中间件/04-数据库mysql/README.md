# 数据库mysql

来源：
- https://campus.wps.cn/contentpreview/27e416d6-31b8-4b29-88fd-328520b1489e

# 数据库基础

本课程将通过实际案例，带你深入了解 MySQL 数据库的使用。我们将围绕一个在线商城项目，学习 MySQL 的各个重要概念和最佳实践。

# Docker 环境下的 MySQL 安装

```
# 拉取MySQL 8.0镜像
docker pull mysql:8.0

# 创建MySQL容器
docker run -d \
    --name mysql8 \
    -p 3306:3306 \
    -e MYSQL_ROOT_PASSWORD=your_password \
    -v mysql_data:/var/lib/mysql \
    mysql:8.0
```

## 验证安装

```
# 查看容器状态
docker ps

# 进入MySQL容器
docker exec -it mysql8 mysql -uroot -p
```

# 在线商城数据库设计

让我们通过设计一个在线商城的数据库来学习 MySQL 的基础概念。

## 需求分析

我们的商城需要管理：

- 用户信息
- 商品信息
- 订单信息
- 购物车信息

## 表设计

```
-- 用户表
CREATE TABLE users (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL COMMENT '用户名',
    password VARCHAR(100) NOT NULL COMMENT '密码',
    email VARCHAR(100) UNIQUE COMMENT '邮箱',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_username (username)  -- 我们稍后会讲解为什么要建立索引
) COMMENT '用户表';

-- 商品表
CREATE TABLE products (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL COMMENT '商品名称',
    price DECIMAL(10,2) NOT NULL COMMENT '商品价格',
    stock INT NOT NULL COMMENT '库存',
    description TEXT COMMENT '商品描述',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_name (name)
) COMMENT '商品表';
```

# MySQL 索引优化

## 为什么需要索引？

让我们做一个小实验：

```
-- 创建测试数据
DELIMITER $$
CREATE PROCEDURE generate_test_data()
BEGIN
    DECLARE i INT DEFAULT 1;
    WHILE i <= 1000000 DO
        INSERT INTO products (name, price, stock, description)
        VALUES (
            CONCAT('商品', i),
            RAND() * 1000,
            FLOOR(RAND() * 1000),
            CONCAT('这是商品', i, '的描述')
        );
        SET i = i + 1;
    END WHILE;
END $$
DELIMITER ;

-- 执行存储过程
CALL generate_test_data();
```

现在让我们比较有索引和无索引的查询性能：

```
-- 无索引查询
SELECT * FROM products WHERE name = '商品999999';

-- 添加索引
CREATE INDEX idx_name ON products(name);

-- 有索引查询
SELECT * FROM products WHERE name = '商品999999';
```

## 索引的最佳实践

1. 选择合适的索引列

   - 常用于 WHERE 子句的列
   - 常用于 JOIN 的列
   - 常用于 ORDER BY 的列
2. 避免过度索引

   - 索引会占用磁盘空间
   - 影响写入性能
   - 增加维护成本

# 实战练习：优化慢查询

## 案例：订单查询优化

```
-- 创建订单表
CREATE TABLE orders (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    total_amount DECIMAL(10,2) NOT NULL,
    status TINYINT NOT NULL COMMENT '1:待付款 2:已付款 3:已发货 4:已完成',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_status (user_id, status)
) COMMENT '订单表';

-- 插入测试数据
INSERT INTO orders (user_id, total_amount, status)
SELECT
    FLOOR(RAND() * 1000),
    RAND() * 1000,
    FLOOR(RAND() * 4) + 1
FROM information_schema.tables A
CROSS JOIN information_schema.tables B
LIMIT 1000000;
```

## 性能分析

使用 EXPLAIN 分析查询计划：

```
-- 分析查询
EXPLAIN SELECT * FROM orders
WHERE user_id = 1 AND status = 2
ORDER BY created_at DESC;

-- 添加优化后的索引
ALTER TABLE orders
ADD INDEX idx_user_status_time (user_id, status, created_at DESC);
```

# 作业练习

1. 在你的 Docker 环境中安装 MySQL
2. 创建上述商城数据库的表结构
3. 插入测试数据并进行性能测试
4. 尝试优化一个复杂的查询场景

提示：可以参考以下场景

- 查询某用户最近一个月的所有订单
- 统计每个商品的销售情况
- 查找最受欢迎的商品

# 小贴士

1. 经常使用 EXPLAIN 分析查询
2. 定期查看慢查询日志
3. 建立合适的索引
4. 注意 SQL 语句的编写规范
5. 适时进行数据库维护
