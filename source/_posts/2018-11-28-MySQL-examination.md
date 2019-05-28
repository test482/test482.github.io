---
title: MySQL 模拟题练习回顾
date: 2018-11-28 20:01:25
updated: 2018-12-7 14:28:24
thumbnail: 
categories: [练习册]
tags: [MySQL]
---

# 实验环境

- MySQL Server 8.0 Oracle Corporation

# 测试题目及题解

## 创建数据库、表及新增用户

1. 创建数据库 *teach_manager* 并新建用户 *test*，密码为 *123*，为新用户授予此数据库上的权限

```sql
-- 创建新用户 test
CREATE USER 'test'@'%' IDENTIFIED BY '123';

-- 创建 teach_manager 数据库
CREATE DATABASE teach_manager;

-- 为新用户授权
GRANT ALL ON teach_manager.* TO 'test'@'%';
```

2. 在新用户模式下，按照要求及说明建立下列所示的表

> **t_teacher 教师表**

| 字段    | 类型        | 说明             |
| ------- | ----------- | ---------------- |
| tea_no  | varchar(12) | 教师编号（主键） |
| name    | varchar(4)  | 姓名 （非空）    |
| sex     | varchar(3)  | 性别：男/女      |
| t_birth | date        | 出生日期         |

```sql
-- t_teacher 教师表代码
CREATE TABLE t_teacher(
  tea_no varchar(12) PRIMARY KEY,
  name varchar(4) NOT NULL,
  sex varchar(3) check(sex in('男','女')),
  t_birth date
);
```

* * *

> **t_course 课程表**

| 字段         | 类型       | 说明             |
| ------------ | ---------- | ---------------- |
| course_no    | varchar(6) | 课程编号（主键） |
| course_name  | varchar(6) | 课程名称（唯一） |
| course_class | int(2)     | 1:必修，2:选修   |

```sql
-- t_course 课程表代码
CREATE TABLE t_course(
  course_no varchar(6) PRIMARY KEY,
  course_name varchar(6) UNIQUE,
  course_class int(2)
);
```

* * *

> **t_teacher_course 任课表**

| 字段       | 类型       | 说明                 |
| ---------- | ---------- | -------------------- |
| tea_no     | varchar(4) | 教师编号（外键）     |
| course_no  | varchar(6) | 课程编号             |
| class_hour | int(2)     | 课时数（默认32学时） |

```sql
-- t_teacher_course 任课表代码
CREATE TABLE t_teacher_course(
  tea_no varchar(4),
  course_no varchar(6),
  class_hour int DEFAULT 32,
  FOREIGN KEY (tea_no) REFERENCES t_teacher(tea_no)
);
```

3. 添加测试数据

> 此处略去插入数据的内容（就是一堆 `insert` 没什么好写的

## 修改数据库

4. 修改『任课表』（`t_teacher_course`），为其设置主键约束，并为 `课程编号` 设置外键约束

```sql
-- 设置主键约束
ALTER TABLE t_teacher_course ADD PRIMARY KEY(tea_no,course_no);

-- 设置“课程编号”外键约束
ALTER TABLE t_teacher_course ADD FOREIGN KEY(course_no) REFERENCES t_course(course_no);
```

5. 为『教师表』（`t_teacher`）增加一个联系电话属性列，要求唯一

```sql
-- 增加“联系电话”字段
ALTER TABLE t_teacher ADD phone_num varchar(11) UNIQUE;
```

## 查询数据库

6. 查询课程名涉及 `科学` 的所有必修课程信息

```sql
SELECT * FROM t_course WHERE course_name LIKE '%科学%';
```

7. 查询每位教师的编号及授课总学时，结果按 授课学时 降序排序

```sql
SELECT tea_no,sum(class_hour) AS 总学时 FROM t_teacher_course 
  GROUP BY tea_no
  ORDER BY 总学时 DESC;
```

8. 查询年龄在 50 岁以上的教师姓名、编号及出生日期

```sql
SELECT name,tea_no,t_birth,year(now())-year(t_birth) AS age
  FROM t_teacher
    WHERE year(now())-year(t_birth)>50;
```

9. 将『语文』课程的课时数在原有的基础上提升 10%

```sql
UPDATE t_teacher_course SET class_hour = class_hour*1.1
  WHERE course_no=
    (SELECT course_no FROM t_course WHERE course_name LIKE '语文');
```

10. 查询每门课程的课程号及该门课程的授课教师人数、授课平均课时数

```sql
SELECT course_no,count(*),avg(class_hour)
  FROM t_teacher_course
    GROUP BY course_no;
```

11. 查询授课门数大于等于 3 门的教师姓名及其授课门数

```sql
SELECT t.num,t_teacher.name
  FROM (SELECT count(course_no) num,tea_no FROM t_teacher_course GROUP BY tea_no) AS t,t_teacher
    WHERE t.num>=2 AND t.tea_no=t_teacher.tea_no;
```

## 视图、存储过程、触发器

12. 创建一个关于『教师授课情况』的视图，包括所有教师的姓名、编号、所授课程的课时和名称，要求视图中体现“参与授课和未授课”的所有教师情况

```sql
CREATE OR REPLACE VIEW teacher_qk
AS
SELECT name,t.tea_no,class_hour,course_name
  FROM t_teacher AS t
    LEFT OUTER JOIN t_teacher_course tc ON t.tea_no=tc.tea_no
    LEFT OUTER JOIN t_course c ON c.course_no=tc.course_no;
```

13. 创建一个存储过程，根据教师编号，输出该教师的授课总课时。最后执行该存储过程（以“t005”为例）

```sql
-- 创建存储过程
DELIMITER //
CREATE PROCEDURE my_pro (IN t_id varchar(4),OUT tname varchar(12),OUT v_ch int)
BEGIN
SELECT sum(class_hour) INTO v_ch FROM t_teacher_course WHERE tea_no=t_id;
SELECT name INTO tname FROM t_teacher WHERE tea_no=t_id;
END
//
```

```sql
-- 调用存储过程
CALL my_pro('t005',@tname,@count);

SELECT @tname,@count;
```

14. 创建一个触发器，当删除课程表中的记录时，将授课信息表中对应的该门课程的相关记录一并删除

```sql
-- 创建触发器
DELIMITER //
CREATE TRIGGER auto_del_course BEFORE DELETE
ON t_course FOR EACH ROW
BEGIN
DELETE FROM t_teacher_course WHERE course_no=old.course_no;
END
//
```

```sql
-- 验证触发器
DELETE FROM t_course WHERE course_no='c00001';
```

# ~~吐槽~~

严厉吐槽我的 MySQL 老师，上课讲的不清不楚，给的模拟题参考答案也是错误百出...... -_-|| 老师你到底有没有试过答案里的代码啊喂？！

不过正好~~借这个机会水篇博客~~。