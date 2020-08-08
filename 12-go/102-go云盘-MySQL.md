# MYSQL服务架构变迁

## 架构变迁

![image-20200725174051284](image-20200725174051284.png)

### 安装模式

- 单点模式

- 主从模式

  >  重放主节点操作

  ![image-20200725174148385](image-20200725174148385.png)

## MySQL主从数据同步

```mysql 
show master status

CHANGE MASTER TO MASTER_HOST='192.168.2.244',MASTER_USER='reader',MASTER_PASSWORD='reader',MASTER_LOG_FILE='binlog.000002',MASTER_LOG_POS=0;
#ifconfig 查找主机 ip
```



从数据库

```mysql
start slave
show slave status\G
```

建表

```mysql
create database testl default character set utf8;
show databases;
use test1;
create table tbl_test (
    'user' varchar(64) not null,
    'age' int(11) not null
)default charset utf8;
```

从数据库查询数据

```mysql
select * from tbl_test
```

### 文件信息表设计

```mysql
-- 创建文件表
CREATE TABLE `tbl_file` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `file_sha1` char(40) NOT NULL DEFAULT '' COMMENT '文件hash',
    `file_name` varchar(256) NOT NULL DEFAULT '' COMMENT '文件名',
    `file_size` bigint(20) DEFAULT '0' COMMENT '文件大小',
    `file_addr` varchar(1024) NOT NULL DEFAULT '' COMMENT '文件存储位置',
    `create_at` datetime default NOW() COMMENT '创建日期',
    `update_at` datetime default NOW() on update current_timestamp() COMMENT '更新日期',
    `status` int(11) NOT NULL DEFAULT '0' COMMENT '状态(可用/禁用/已删除等状态)',
    `ext1` int(11) DEFAULT '0' COMMENT '备用字段1',
    `ext2` text COMMENT '备用字段2',
    PRIMARY KEY (`id`),
    UNIQUE KEY `idx_file_hash` (`file_sha1`),
    KEY `idx_status` (`status`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

### 水平分表

假设分成256张文件表，按文件Sha1值后两位来切分则以:` tbl_${file_sha1}[:-2]`的规则到对应表进行存取

![image-20200727172438971](image-20200727172438971.png)

## Golang操作MySQL

- 使用Go标准接口

### 封装mysql连接

`db\mysql\conn.go`

```go
package mysql

import (
   "database/sql"
   "fmt"
   "os"


   _"github.com/go-sql-driver/mysql"
)

var db *sql.DB

func init(){
   db,_=sql.Open("mysql","root:root@tcp(127.0.0.1:3307)/fileserver?charset=utf8")
   db.SetMaxOpenConns(1000)
   err:=db.Ping()
   if err!=nil {
      fmt.Printf("Failed to connect to mysql,err"+err.Error())
      os.Exit(1)
   }
}

//外部接口 返回数据库连接对象
func DBConn()*sql.DB{
   return db
}
```



### 封装文件上传model层方法

`db\file.go`

```go
package db

import (
   mydb "../db/mysql"
   "fmt"
)

// 文件上传完成 并保存 meta
func OnFIleUploadFinished(filehash string,filename string, filesize int64, fileaddr string) bool {
   stmt, err := mydb.DBConn().Prepare(
      "insert ignore into tbl_file (`file_sha1`,`file_name`,`file_size`," +
         "`file_addr`,`status`) values (?,?,?,?,1)")
   if err != nil {
      fmt.Println("Failed to prepare statement, err:" + err.Error())
      return false
   }
   defer stmt.Close()

   ret, err := stmt.Exec(filehash, filename, filesize, fileaddr)
   if err != nil {
      fmt.Println(err.Error())
      return false
   }
   if rf, err := ret.RowsAffected(); nil == err {
      if rf <= 0 {//没有新的记录
         fmt.Printf("File with hash:%s has been uploaded before", filehash)
      }
      return true
   }
   return false
}
```

### controller层调用

```go
// 上传meta信息入库
func UpdateFileMetaDB(fmeta FileMeta) bool{
	res := db.OnFIleUploadFinished(fmeta.FileSha1, fmeta.FileName, fmeta.FileSize, fmeta.Location)
	return res
}
```

## 总结

- 通过sqI.DB来管理数据库连接对象
- 通过`sql.Open`来创建`协程安全`的sqI.DB对象
- 优先使用`Prepared Statement`