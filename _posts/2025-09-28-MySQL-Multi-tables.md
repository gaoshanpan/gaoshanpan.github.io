---
title: "MySQL multi-tables operations"
date: 2025-09-25 10:00:00 +0800
categories: [CS basics, Mysql] # [Top_category, sub_category]
tags: [sql]      # TAG names should always be lowercase
# author:gaoshan
---


```sql
-- -----------------多表查询: 交叉查询-----------------
-- 格式: select * from 表A, 表B;
-- 结果: 两张表的笛卡尔积, 即:表A的总条数 * 表B的总条数
select * from hero, kongfu; --产生大量的脏数据, 实际开发不用
INSERT INTO table_name (column1, column2, column3, column4)
VALUES
  (01, 183, 9000, 200),
  (01, 183, 9000, 200),
  (02, 184, 9001, 450),
  (03, 185, 9002, 980),
  (04, 186, 9003, 450),
  (05, 187, 9004, 980);
-- 合并插入 比 多条单独插入 更高效.只需要执行一次 SQL 命令。
-- 减少网络传输次数。
-- 插入速度提升明显（尤其是上千条记录时）。
```

![img-description](/assets/img/cartesian_product.jpg)

```sql
-- -----------------多表查询: 连接查询-----------------
-- 场景1: 内连接, 查询结果: 表的交集.
-- 格式1: 显式内连接, select * from A inner join B on 关联条件 where ...;
select * from hero h inner join kongfu kf on h.kongfu_id = kf.kid; 
select * from hero h join kongfu kf on h.kongfu_id = kf.kid;  -- inner 和alias的as都可以忽略

-- 格式2: 隐式内连接, select * from A, B where 关联条件; 但不推荐使用
select * from hero h, kongfu kf where h.kongfu_id = kf.kid;

-- 显示内连接和隐式内连接的功能上完全一致，但写法和语义上有区别:
-- 两句在数据库执行时，内部机制是一样的，数据库优化器会把它们都转换为“内连接运算（INNER JOIN）”，所以输出的结果行、顺序、执行效率——都相同.

-- 场景2: 外连接.
-- 格式1: 左外连接, select * from A left outer join B on 关联条件 where ...;
select * from hero h left outer join kongfu kf on h.kongfu kf = kf.kid; 
select * from hero h left join kongfu kf on h.kongfu kf = kf.kid; --outer可以忽略

-- 格式2: 右外连接, select * from A, B where ...; --不想用
select * from hero h right outer join kongfu kf on h.kongfu kf = kf.kid; 
select * from hero h right join kongfu kf on h.kongfu kf = kf.kid; --outer可以忽略


```

```sql
-- -------------- 案例1: 多表建表之 1对多 --------------
/*
约束回顾:
    概述:
        就是用来保证数据的完整性 和 安全性的.
    分类:
        单表约束:
            主键约束: primary key, 一般结合自增(auto_increment) 使用.
            非空约束: not null
            唯一约束: unique
            默认约束: default
        多表约束:
            外键约束: foreign key

多表约束详解:
    概述:
        它是用来描述多表关系的, 一般在 外表(从表)中 做限定.
    格式:
        场景1: 建表时创建, 和以前写字段的方式一样.
            [constraint 外键约束名] foreign key(外键列名) references 主表名(主键列名)
        场景2: 建表后创建.
            alter table 表名 add [constraint 外键约束名] foreign key(外键列名) references 主表名(主键列名);
        场景3: 删除外键约束.
            alter table 表名 drop foreign key 外键约束名;
    注意事项:
        1. 多表关系之 一对多建表原则: 在"多"的一方新建一列, 充当外键列, 去关联"一"的一方的主键列.
        2. 多表关系中 有外键的表称之为: 外表(从表), 有主键的表称之为: 主表.
        3. 外键约束特点(记忆): 外表的外键列 不能出现 主表的主键列 没有的数据.
*/
-- 1. 建库, 切库, 查表.
drop database if exists day03;
create database if not exists day03;
use day03;
show tables;

-- 2. 建表, 部门表.
create table dept(
    id int primary key auto_increment,    -- 部门id, 主键, 自增
    name varchar(10)                      -- 部门名
);
-- 3. 建表, 员工表, 先不添加约束, 我们插入数据看看.
create table employee(
    id int primary key auto_increment,  -- 员工id
    name varchar(20),                   -- 员工姓名
    salary int,                         -- 工资
    dept_id int,                         -- 部门id
    constraint fk_dept_em foreign key(dept_id) references dept(id)  -- 设置员工表和部门表的 外键约束
);

-- 4. 给部门表添加数据.
insert into dept values(null, '人事部'), (null, '财务部'), (null, '研发部');
-- 5. 给员工表添加数据, 发现, 数据可以任意加入, 还可以出现: 员工所在的部门, 居然是部门表没有的部门 => 脏数据.
insert into employee values(null, '刘备', 5000, 1);
insert into employee values(null, '关羽', 50000, 3);
insert into employee values(null, '小乔', 66666, 9);

-- 6. 删表, 重新建表, 建表时: 添加外键约束.
drop table dept;
drop table employee;

-- 7. 再次给 部门表 和 员工表添加数据, 发现: OK了, 员工所在的部门, 必须是 部门表已有的部门.
insert into dept values(null, '人事部'), (null, '财务部'), (null, '研发部');
insert into employee values(null, '刘备', 5000, 1);
insert into employee values(null, '关羽', 50000, 3);
insert into employee values(null, '小乔', 66666, 9);  -- 报错, 外表的外键列 不能出现 主表的主键列没有的数据.

-- 8. 手动删除 外键约束, 观察: 表关系.
alter table employee drop foreign key fk_dept_em;

-- 9. 手动再次添加 外键约束, 即: 建表后, 添加外键约束.   掌握.
alter table employee add foreign key(dept_id) references dept(id);

-- 10.查看表数据.
select * from dept;
select * from employee;
delete from employee where id = 4;



-- -------------- 案例2: 多表查询之 交叉查询 和 连接查询(内、外) 详解 --------------
/*
多表查询解释:
    记忆(精髓):
        多表查询的本质就是 根据关联条件 把多张表变成1张表, 然后进行 单表查询.
    分类:
        交叉查询:
        连接查询:
            内连接:
            外连接:
            扩展: 自关联(自连接)查询
        子查询:
*/
-- 1. 建表.
-- 创建hero表
CREATE TABLE hero(
    hid   INT PRIMARY KEY,  -- 英雄id
    hname VARCHAR(255),     -- 英雄名
    kongfu_id INT           -- 功夫id
);

-- 创建kongfu表
CREATE TABLE kongfu (
    kid     INT PRIMARY KEY,    -- 功夫id
    kname   VARCHAR(255)        -- 功夫名
);

-- 2. 添加表数据.
-- 插入hero数据
INSERT INTO hero VALUES(1, '鸠摩智', 9),(3, '乔峰', 1),(4, '虚竹', 4),(5, '段誉', 12);
-- 插入kongfu数据
INSERT INTO kongfu VALUES(1, '降龙十八掌'),(2, '乾坤大挪移'),(3, '猴子偷桃'),(4, '天山折梅手');

-- 3. 查看表数据.
select * from hero;     -- 英雄表.
select * from kongfu;   -- 功夫表

-- 4. 交叉查询, 查询结果是: 两张表的笛卡尔积, 即: 表A的总条数 * 表B的总条数, 会有大量的脏数据, 实际开发, 一般不用.
-- 格式: select * from 表A, 表B;
select * from hero, kongfu;

-- 5. 内连接, 查询结果是: 表的交集.
-- 场景1: 显式内连接. 格式: select * from 表A inner join 表B on 关联条件 where...;
-- 特点： 使用JOIN关键字，明确指出连接的类型，如INNER JOIN（内连接）、LEFT JOIN（左连接）、RIGHT JOIN（右连接）等。
select * from hero h inner join kongfu kf on h.kongfu_id = kf.kid;
select * from hero h join kongfu kf on h.kongfu_id = kf.kid;  -- inner 可以省略不写.

输入：
Sales 表：
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
Product 表：
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+
select * from Sales inner join Product on Sales.product_id=Product.product_id;

| sale_id | product_id | year | quantity | price | product_id | product_name |
| ------- | ---------- | ---- | -------- | ----- | ---------- | ------------ |
| 2       | 100        | 2009 | 12       | 5000  | 100        | Nokia        |
| 1       | 100        | 2008 | 10       | 5000  | 100        | Nokia        |
| 7       | 200        | 2011 | 15       | 9000  | 200        | Apple        |

select * from Sales left join Product on Sales.product_id=Product.product_id;
| sale_id | product_id | year | quantity | price | product_id | product_name |
| ------- | ---------- | ---- | -------- | ----- | ---------- | ------------ |
| 1       | 100        | 2008 | 10       | 5000  | 100        | Nokia        |
| 2       | 100        | 2009 | 12       | 5000  | 100        | Nokia        |
| 7       | 200        | 2011 | 15       | 9000  | 200        | Apple        |

select * from Sales right join Product on Sales.product_id=Product.product_id;
| sale_id | product_id | year | quantity | price | product_id | product_name |
| ------- | ---------- | ---- | -------- | ----- | ---------- | ------------ |
| 2       | 100        | 2009 | 12       | 5000  | 100        | Nokia        |
| 1       | 100        | 2008 | 10       | 5000  | 100        | Nokia        |
| 7       | 200        | 2011 | 15       | 9000  | 200        | Apple        |
| null    | null       | null | null     | null  | 300        | Samsung      |

-- 场景2: 隐式(没join)内连接. 格式: select * from 表A, 表B  where 关联条件...; 一般不用, 因为避免使用时忘记加where而导致笛卡尔积.
select * from hero h, kongfu kf where h.kongfu_id = kf.kid;

-- 6. 外连接.
-- 场景1: 左外连接, 查询结果是: 左表全集 + 交集.
-- 格式: select * from 表A left outer join 表B on 关联条件 where...;
select * from hero h left outer join kongfu kf on h.kongfu_id = kf.kid;
select * from hero h left join kongfu kf on h.kongfu_id = kf.kid;     -- outer可以省略不写.

-- 场景2: 右外连接, 查询结果是: 右表全集 + 交集.
-- 格式: select * from 表A right outer join 表B on 关联条件 where...;
select * from hero h right outer join kongfu kf on h.kongfu_id = kf.kid;
select * from hero h right join kongfu kf on h.kongfu_id = kf.kid;     -- outer可以省略不写.

```
![img-description](/assets/img/join_m.jpg)

```sql
-- -------------- 案例3: 多表查询之 子查询 详解 --------------
/*
子查询介绍:
    概述:
        实际开发中, 如果1个查询语句的 查询条件需要依赖 另一个查询语句的查询结果, 这种写法就叫: 子查询.
        即: 1个SQL的查询条件, 依赖另1个SQL语句的查询结果.
        外边的查询叫: 主查询(父查询), 里边的查询叫: 子查询.
    格式:
        |--------- 主查询 ---------- |  |-------- 子查询 ----------|
        select * from 表A where 字段 > (select 列名 from 表B where ....);
*/

-- 1. 建表.
create table category (             -- 分类表
  cid varchar(32) primary key ,     -- 分类id
  cname varchar(50)                 -- 分类名
);
create table products(              -- 商品表
  pid varchar(32) primary key ,     -- 商品id
  pname varchar(50),                -- 商品名
  price int,                        -- 商品价格
  flag varchar(2),                  -- 是否上架标记为：1表示上架、0表示下架
  category_id varchar(32),
  constraint products_fk foreign key (category_id) references category (cid)
);

-- 2. 添加表数据.
--分类
INSERT INTO category(cid,cname) VALUES('c001','家电');
INSERT INTO category(cid,cname) VALUES('c002','服饰');
INSERT INTO category(cid,cname) VALUES('c003','化妆品');
INSERT INTO category(cid,cname) VALUES('c004','奢侈品');

--商品
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p001','联想',5000,'1','c001');
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p002','海尔',3000,'1','c001');
INSERT INTO products(pid, pname,price,flag,category_id) VALUES('p003','雷神',5000,'1','c001');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p004','JACK JONES',800,'1','c002');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p005','真维斯',200,'1','c002');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p006','花花公子',440,'1','c002');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p007','劲霸',2000,'1','c002');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p008','香奈儿',800,'1','c003');
INSERT INTO products (pid, pname,price,flag,category_id) VALUES('p009','相宜本草',200,'1','c003');

-- 3. 查看表数据.
select * from category;
select * from products;


-- 4. 子查询演示.
-- 需求1: 查询哪些分类的商品已经上架
-- Step1: 查询上架的商品, 的 分类id
select distinct category_id from products where flag = 1;

-- Step2: 根据上一步查出的 分类id, 找其对应的 分类名.
select * from category where cid in ('c001', 'c002', 'c003');

-- Step3: 把上述的分解步骤, 合并到一起, 就是: 子查询.
select * from category where cid in (
    select distinct category_id from products where flag = 1
);

-- 需求2: 查询所有分类商品的个数
select
    cname,
    count(category_id) total_cnt,        -- 基于业务, 这里写 category_id 更合适.
    count(pid) total_cnt2                -- 基于效率, 这里写 pid 更合适.
from
    category c
left join products p on c.cid = p.category_id   -- 外连接查询
group by cname;


-- -------------- 案例4: 多表查询之 自关联(自连接)查询 详解 --------------
-- 概述: 表自己和自己做关联查询, 就称之为: 自连接(自关联)查询.  一般用于: 省市区三级联动.
-- 1. 建表, 插入表数据, 直接运行 areas.sql 脚本文件即可.

-- 2. 查看表数据.
select * from areas;

-- 3. 初始 区域表的数据.
select * from areas where id = '410000';        -- 查看 河南省 的信息
select * from areas where pid = '410000';       -- 查看 河南省 所有的市的信息
select * from areas where pid = '410700';       -- 查看 河南省新乡市 所有的县区信息


-- 4. 查看所有省的, 所有市的, 所有县区的信息.
select
    province.id, province.title,        -- 省的id, 省的名字
    city.id, city.title,                -- 市的id, 省的名字
    county.id, county.title             -- 县区的id, 省的名字
from
    areas as county                                 -- 县区表
join areas as city on county.pid = city.id          -- 市表
join areas as province on city.pid = province.id;   -- 省表

-- 5. 在上述查询基础上, 查看: 河南省所有的信息
select
    province.id, province.title,        -- 省的id, 省的名字
    city.id, city.title,                -- 市的id, 省的名字
    county.id, county.title             -- 县区的id, 省的名字
from
    areas as county                                 -- 县区表
join areas as city on county.pid = city.id          -- 市表
join areas as province on city.pid = province.id   -- 省表
where province.title='河南省';



-- 6. 在上述查询基础上, 查看: 新乡市所有的信息
select
    province.id, province.title,        -- 省的id, 省的名字
    city.id, city.title,                -- 市的id, 省的名字
    county.id, county.title             -- 县区的id, 省的名字
from
    areas as county                                 -- 县区表
join areas as city on county.pid = city.id          -- 市表
join areas as province on city.pid = province.id   -- 省表
where city.title='新乡市';





-- -------------- 案例5: 多表建表之 多对多 详解 --------------
-- 建表原则: 新建中间表, 该表至少有2列, 充当外键列, 分别去关联"多"的两方的主键列.
-- 1. 建表.
-- 学生表
create table student(
    sid int primary key auto_increment,   -- 学生id, 主键, 自增
    name varchar(10)                      -- 学生姓名
);
-- 选修课表
create table course(
    cid int primary key auto_increment,   -- 选修课id
    name varchar(10)                      -- 选修课名
);
-- 中间表
create table stu_cur(
    id int not null unique auto_increment,    -- 中间表, 自身id, 伪主键(唯一, 非空, 自增)
    sid int,                                  -- 学生id
    cid int                                   -- 选修课id
    -- primary key(sid, cid)                     -- 联合主键, 建表时添加.
);

-- 2. 添加外键约束.
-- 中间表 和 学生表
alter table stu_cur add constraint fk_stu_middle foreign key (sid) references student(sid);
-- 中间表 和 选修课表
alter table stu_cur add constraint fk_cur_middle foreign key (cid) references course(cid);

-- 3. 设置中间表的 sid, cid列为: 联合主键.
alter table stu_cur add primary key(sid, cid);

-- 4. 添加表数据.
-- 学生表
insert into student values(null, '曹操'), (null, '夏侯惇'), (null, '吕布');
-- 选修课表
insert into course values(null, 'AI'), (null, 'Python大数据'), (null, '鸿蒙');
-- 中间表
insert into stu_cur values(null, 1, 1);     -- 曹操 学 AI
insert into stu_cur values(null, 1, 2);     -- 曹操 学 Py大数据
insert into stu_cur values(null, 2, 1);     -- 夏侯惇 学 AI
insert into stu_cur values(null, 2, 1);     -- 夏侯惇 学 AI, 报错. 联合主键

-- 5. 查询结果.
select * from student;
select * from course;
select * from stu_cur;

-- 6. 扩展, 查看每个学生的选课情况.
select
    s.sid, s.name,      -- 学生id, 学生名
    c.cid, c.name       -- 选修课id, 课程名
from
    student s
left join stu_cur sc on s.sid = sc.sid
left join course c on sc.cid = c.cid;

-- 7. 统计每个学生学了几门课.
select
    s.name, count(c.cid) as total_cnt
from
    student s
left join stu_cur sc on s.sid = sc.sid
left join course c on sc.cid = c.cid
group by s.name;

-- 8. 统计选修了2门课及其以上的学生姓名.
select
    s.name, count(c.cid) as total_cnt
from
    student s
left join stu_cur sc on s.sid = sc.sid
left join course c on sc.cid = c.cid
group by s.name         -- 根据 学生名 分组
having total_cnt >= 2;  -- 组后筛选, 选修课数 大于等于 2
```

![img-description](/assets/img/join.jpg)