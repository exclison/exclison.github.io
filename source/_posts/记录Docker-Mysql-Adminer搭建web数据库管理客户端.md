---
title: 记录Docker+Mysql+Adminer搭建web数据库管理客户端
date: 2022-11-16 16:56:20
tags: docker mysql
---
# 数据持久化-MySQL

## Adminer

Adminer是一个类似于phpMyAdmin的MySQL管理客户端。整个程序只有一个PHP文件，易于使用和安装。Adminer支持多语言（已自 带11种翻译语言文件，可以按自己的需求翻译相应的语言）。支持PHP4.3+，MySQL 4.1+以上的版本。

提供的功能包括：

1：创建，修改，删除索引/外键/视图/存储过程和函数。
2：查询，合计，排序数据。
3：新增/修改/删除记录。
4：支持所有数据类型，包括大字段。
5：能够批量执行SQL语句。
6：支持将数据，表结构，视图导成SQL或CSV。
7：能够外键关联打印数据库概要。
8：能够查看进程和关闭进程。
9：能够查看用户和权限并修改。
10：管理事件和表格分区（MySQL5.1才支持）



类似的还有phpAdmin



## Docker + Mysql + Adminer

创建mysql目录

在mysql目录下建立docker-compose.yml文件(注意:文件名必须为docker-compose.yml)

```bash
cd /usr/local/docker
mkdir mysql
vim docker-compose.yml
```

将下面的配置文件拷贝到docker-compose.yml中

:wq退出

```yaml
version: '3.1'
services:
  db:
    image: mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 123456
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4
      --collation-server=utf8mb4_general_ci
      --explicit_defaults_for_timestamp=true
      --lower_case_table_names=1
    ports:
      - 3306:3306
    volumes:
      - ./data:/var/lib/mysql

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

执行docker-compose.yml(-d为在后台运行)

```bash
docker-compose up -d
```

打开

Adminer数据库管理工具

http://localhost:8080

输入账号root,密码123456,登录进入管理界面



## nodejs原生驱动

使用mysql2js 提供promise可以避免回调地狱

```javascript
(async () => {

    const mysql = require('mysql2/promise')
    //连接设置
    const cfg = {
        host: 'localhost',
        user: 'root',
        password: '123456',
        database: 'testnode'
    }

    const connection = await mysql.createConnection(cfg)//创建连接

    const CRTATE_SQL = `CREATE TABLE IF NOT EXISTS test (
                        id INT NOT NULL AUTO_INCREMENT,
                        message VARCHAR(45) NULL,
                        PRIMARY KEY (id))`//建立test表SQL

    let ret = await connection.execute(CRTATE_SQL)//只想SQL
    console.log('create',ret)

    const INSERT_SQL = `INSERT INTO test(message) VALUES(?)`

    ret = await connection.execute(INSERT_SQL,['ABC'])//插入数据
    console.log('insert',ret)

    const [rows,fields] = await connection.execute(`SELECT * FROM test`)//查询数据

    console.log('select:',JSON.stringify(rows))
    
})()
```



## nodejs ORM-Sequelize

### ORM 框架

对象关系映射(Object Relational Mapping,简称ORM)模式是一种为了解决面向对象与关系型数据库存在的互不匹配的现象的技术.简单的说，ORM是通过使用描述对象和数据库之间映射的元数据，将程序中的对象自动持久化到关系数据库中.

### Sequelize

Sequelize是用于Postgres，MySQL，MariaDB，SQLite和Microsoft SQL Server的基于约定的Node.js ORM。它具有可靠的事务支持，关系，渴望和延迟加载，读取复制等功能。

```javascript
(async () => {
    const Sequelize = require('sequelize')

    const sequelize = new Sequelize('testnode','root','123456',{
        host:'localhost',
        dialect:'mysql',
    })

    //定义模型
    const Fruit = sequelize.define('Fruit',{
        name:{type:Sequelize.STRING(20),allowNull:false},
        price:{type:Sequelize.FLOAT,allowNull:false},
        stock:{type:Sequelize.INTEGER,defaultValue:0}
    })

    let ret = await Fruit.sync({force:true})

    ret = await Fruit.create({
        name:'香蕉',
        price:3.5
    })
    console.log('create',ret)

    ret = await Fruit.update({
        price:4
    },{
        where:{name:'香蕉'}
    })

    const Op = Sequelize.Op
    ret = await Fruit.findAll({
        where:{price:{[Op.lt]:5,[Op.gt]:2}}
    })
    console.log('select',ret)

})()
```

### UUID-主键

```javascript
id:{
            type:Sequelize.DataTypes.UUID,
            defaultValue:Sequelize.DataTypes.UUIDV1,
            primaryKey:true
        },
```

