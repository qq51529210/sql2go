# sql2go
这是一个生成golang数据库访问代码的工具。比如，
## 使用
1. 编译[main](./main)包，得到执行的程序。
2. main --config cfg.json

## 程序配置
下面是配置文件的例子，字段有解析。
```json
{
  "?": "数据库连接字符串，url.Schema指定driver，目前只有mysql",
  "dbUrl": "mysql://root:123456@tcp(192.168.1.66)/pro_rbac",
  "?": "生成代码文件路径，空则使用程序当前目录+数据库名.go",
  "file": "dao",
  "?": "db代码包名，空则使用文件名称",
  "pkg": "dao",
  "?": "生成Query函数",
  "query": [
    {
      "func": "UserCount",
      "?": "返回单值而不是数组",
      "row": true,
      "sql": [
        "select count(id) from user where id>{id:int64}"
      ]
    },
    {
      "func": "UserList",
      "?": "生成的结构体字段类型为sql.NullX",
      "null": true,
      "sql": [
        "select * from user where id>{id:int64}"
      ]
    },
    {
      "func": "UserSearchByNameLike",
      "sql": [
        "select * from user where name like {name:string}",
        "order by [order:id] limit {begin:int64}, {total:int64}"
      ]
    },
    {
      "func": "AppRoleAccessList",
      "sql": [
        "select id,name,access from",
        "(select id,name from res where app_id={appId:int64}) a",
        "left join",
        "(select res_id,access from role_res where role_id={roleId:int64}) b",
        "on a.id = b.res_id",
        "order by [order:id] [sort:desc] limit {begin:int64},{total:int64}"
      ]
    }
  ],
  "?": "生成Exec函数",
  "exec": [
    {
  	  "?": "函数的名称",
      "func": "UserDeleteById",
  	  "?": "sql，写多行，避免过长不好阅读",
      "sql": [
        "delete from user where id={id:int64}"
      ]
    },
    {
      "func": "UserInsert",
      "sql": [
        "insert into user(name,password,email,mobile,state)",
        "values({name:string},{password:string},{email:string},{mobile:string},1)"
      ]
    },
    {
      "func": "UserUpdate",
      "sql": [
        "update user set password={password:string} where id={id:int64}"
      ]
    }
  ]
}
```
## 下一步
实现http方式的在线生成
