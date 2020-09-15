# 概述

每天我们的很多工作都在和数据库来打交道，进行CRUD，但是你知道怎么用jdbc来启动一个事务吗？ ：D 

这个问法本身就有问题哈，任何我们利用jdbc进行的数据库操作都在一个事务当中，包括当我们执行一个查询语句时，查询语句也是运行在事务当中。但是你知道怎么利用jdbc将多个操作放入一个事务中吗？

# 怎样启动一个事务

操作数据库首先要获得Connecton，Connection默认是auto commit(自动提交的)， 也就是当你执行一个插入语句时， 执行完之后是立马提交的。假设你的需求需要执行多个插入更新操作而且这些更新插入操作要作为一个事务，要么全部成功要么全部失败，应该怎么做呢？

自然是首先关闭自动提交模式， 然后执行插入更新操作， 然后commit， 如果期间出现异常则rollback, 最终关闭连接。样例代码如下
```java
    Connection connection = getConnection();
    try {
        boolean autoCommit = connection.getAutoCommit();
        connection.setAutoCommit(false);

        // operation1
        
        // operation2
        
        connection.commit();
    } catch (Exception e) {
        connection.rollback();
    } finally {
        connection.setAutoCommit(autoCommit);
        connection.close();
    }

```

# 事务的隔离级别

我们已经知道了如何将多个数据库操作放到一个事务中，但是你知道怎么设置事务的隔离级别？都有哪几种事务隔离级别吗？

## 何谓事务的隔离

所有的数据库操作都运行在事务当中，理想情况下，这些事务之间应该彼此不受影响。事务的隔离就是指多个事务在并发执行时，事务之间是如何的影响彼此的。影响的程度又引出了事务的隔离级别。数据库系统是一个共享资源，是被并发访问的，级别越高，需要耗费的代价越大。

## 事务隔离级别种类

1. Read Uncommited
2. Read Commited
3. Repeatable Read
4. Serializable

## 设置事务的隔离级别
```java
    Connection.setTransactionIsolation
```

# ORM 框架如何实现事务的封装

知道了前面的介绍，我门就理解了ORM 框架是如何来封装事务的了，没有什么神秘的东西。

# Spring 事务

且听下回分解：D