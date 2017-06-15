##设置
---
sequelize 会在初始化的时候创建一个连接池，我们在单进程链接数据库的时候，要保证只创建一个连接池，如果是多进程应用，每个进程都需要一个连接池，并且在设置最大连接数时候要把每个子进程的连接数加到一起来计算最大连接数。

```
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'mysql'|'sqlite'|'postgres'|'mssql',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: 'path/to/database.sqlite'
});

// Or you can simply use a connection uri
const sequelize = new Sequelize('postgres://user:pass@example.com:5432/dbname');
```
sequelize 构造函数option选项还有更多设置，参考 [详细文档](http://docs.sequelizejs.com/class/lib/sequelize.js~Sequelize.html)

[page](./tt.md);