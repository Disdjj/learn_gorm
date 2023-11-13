> 项目中GORM的使用非常广泛, 从2023/11/13开始, 计划对其源码进行一次比较深入的探究, 以此来提高自己对于MySQL以及GORM的理解水平.

# 环境准备
  1. Docker

      为了能够在本地快速的启动以及运行MySQL, 启动一个容器服务是非常有必要的

      启动的命令如下

```SQL
docker run--env=MYSQL_ROOT_PASSWORD=djj --env=PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin --volume=/var/lib/mysql -p 3306:3306 -p 33060:33060 --runtime=runc -d mysql:latest
```

      DSN: root:djj@localhost:3306/Test
  2. 源码准备

      [https://github.com/go-gorm/gorm.git](https://github.com/go-gorm/gorm.git)
  3. IDE

      使用GoLand作为本地开发的IDE
  4. 大模型

      使用GPT-4 turbo 搭配chatbox作为主要的debug以及分析工具
  5. 笔记

      好记性不如烂笔头, 使用wolai作为笔记来记录自己学习到的知识和自己的想法.

# Hello, World

```SQL
package main

import (
  "gorm.io/gorm"
  "gorm.io/driver/sqlite"
)

type Product struct {
  gorm.Model
  Code  string
  Price uint
}

func main() {
  db, err := gorm.Open(sqlite.Open("test.db"), &gorm.Config{})
  if err != nil {
    panic("failed to connect database")
  }

  // 迁移 schema
  db.AutoMigrate(&Product{})

  // Create
  db.Create(&Product{Code: "D42", Price: 100})

  // Read
  var product Product
  db.First(&product, 1) // 根据整型主键查找
  db.First(&product, "code = ?", "D42") // 查找 code 字段值为 D42 的记录

  // Update - 将 product 的 price 更新为 200
  db.Model(&product).Update("Price", 200)
  // Update - 更新多个字段
  db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // 仅更新非零值字段
  db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})

  // Delete - 删除 product
  db.Delete(&product, 1)
}
```

  以上代码是GORM官方的hello, world教程, [https://gorm.io/zh_CN/docs/](https://gorm.io/zh_CN/docs/)

   我们可以从中一窥主要的功能
    1. 连接数据库
    2. 模型迁移
    3. CRUD

# 大纲

  > 基本按照GORM官方文档, 进行由深及浅的源码阅读, 以期能够获得最平滑的学习曲线.

  ## CRUD

    [连接数据库](https://www.wolai.com/n1J6M8GJJA69khC2UbPEq4)

    model

    查询

    高级查询

    更新

    删除

    SQL相关

  ## 关联

    Belongs to

    Has One

    HasMany

    Many 2 Many

    预加载

  # 高阶

    context

    error handle

    链式处理

    Session

    hook

    *事务

    迁移

    log

    DB 接口

    性能

    自定义数据类型

  ## 高级能力

    database Resolver

    sharding

    序列化

    Prometheus

    索引

    约束

    复合主键

    安全性

    插件

    driver