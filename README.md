# SpringCloudAlibaba-Seata-Nacos
### 分布式事务Seata
| 组件 | 版本 |  
| :------------- | :----------: | 
| Spring Boot | 2.2.5.RELEASE |
|Spring Cloud|Hoxton.SR3|
|Spring Cloud Alibaba|2.2.1.RELEASE|
|Nacos|1.2.1|
|Seata|1.4.2|


### seata-server 1.4.2 服务搭建
    file.conf 配置文件
     
    store {
      mode = "db"
      db {
        datasource = "druid"
        dbType = "mysql"
        driverClassName = "com.mysql.cj.jdbc.Driver"
        url = "jdbc:mysql://localhost:3306/seata?rewriteBatchedStatements=true&serverTimezone=GMT"
        user = "user"
        password = "pwd"
        minConn = 5
        maxConn = 100
        globalTable = "global_table"
        branchTable = "branch_table"
        lockTable = "lock_table"
        queryLimit = 100
        maxWait = 5000
      }
    }
    
    registry.conf 配置文件
    
    registry {
      type = "nacos"
    
      nacos {
        application = "seata-server"
        serverAddr = "localhost:8848"
        group = "SEATA_GROUP"
        namespace = ""
        cluster = "default"
        username = ""
        password = ""
      }
    }
    
    config {
      type = "nacos"
   
      nacos {
        serverAddr = "localhost:8848"
        namespace = ""
        group = "SEATA_GROUP"
        username = ""
        password = ""
        dataId = "seataServer.properties"
      }
    }
    
    启动server
    ./seata-server.sh -h 外网ip -p 8091
    

### Nacos 
    创建dataId=seataServer.properties, Group=SEATA_GROUP的配置,添加如下内容:
    
    # seata-account-group 与微服务中的seata.tx-service-group配置一致 
    service.vgroupMapping.seata-account-group=default
    service.vgroupMapping.seata-order-group=default
    service.vgroupMapping.seata-storage-group=default
    
    store.mode=db
    store.lock.mode=db
    store.session.mode=db
    
    store.db.datasource=druid
    store.db.dbType=mysql
    store.db.driverClassName=com.mysql.cj.jdbc.Driver
    store.db.url=jdbc:mysql://localhost:3306/seata?rewriteBatchedStatements=true&serverTimezone=GMT
    store.db.user=xxx
    store.db.password=xxx
    store.db.minConn=5
    store.db.maxConn=30
    store.db.globalTable=global_table
    store.db.branchTable=branch_table
    store.db.distributedLockTable=distributed_lock
    store.db.queryLimit=100
    store.db.lockTable=lock_table
    store.db.maxWait=5000
    client.undo.dataValidation=true

### MySQL
    执行seata.sql脚本，创建seata数据库
    在每个微服务库中执行undo_log.sql 用与seata纪录和回滚数据
    
### 启动测试
    http://localhost:8180/order/create?userId=1&productId=1&count=10&money=100