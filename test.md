## PostgreSQL 过程语言安装问题（Resolved）

#### 问题描述

对于PostgreSQL 安装过程语言如tcl, python等出错，问题和实验过程如下：
```shell
	版本：Ubuntu：18.04；PostgreSQL：10.10
	安装相关包：sudo apt-get install postgresql-contrib
	重启系统：systemctl restart postgresql-10
	重启后进入控制台：create extension pltcl;
		得到： could not open extension control file "/usr/share/postgresql/10/extension/pltcl.control": No such file or directory
	创建语言：create language pltcl;
		得到： could not access file "$libdir/pltcl": No such file or directory
```
未能在`/usr`文件夹下找到相关` .control` 文件，但是访问表格`pg_pltemplate`得到的信息如下：
| tmplname   | tmpltrusted | tmpldbacreate | tmplhandler            | tmplinline               | tmplvalidator       | tmpllibrary       | tmplacl |
| ---------- | ----------- | ------------- | ---------------------- | :----------------------- | ------------------- | ----------------- | ------- |
| plpgsql    | t           | t             | plpgsql_call_handler   | plpgsql_inline_handler   | plpgsql_validator   | $libdir/plpgsql   |         |
| pltcl      | t           | t             | pltcl_call_handler     |                          |                     | $libdir/pltcl     |         |
| pltclu     | f           | f             | pltclu_call_handler    |                          |                     | $libdir/pltcl     |         |
| plperl     | t           | t             | plperl_call_handler    | plperl_inline_handler    | plperl_validator    | $libdir/plperl    |         |
| plperlu    | f           | f             | plperlu_call_handler   | plperlu_inline_handler   | plperlu_validator   | $libdir/plperl    |         |
| plpythonu  | f           | f             | plpython_call_handler  | plpython_inline_handler  | plpython_validator  | $libdir/plpython2 |         |
| plpython2u | f           | f             | plpython2_call_handler | plpython2_inline_handler | plpython2_validator | $libdir/plpython2 |         |
| plpython3u | f           | f             | plpython3_call_handler | plpython3_inline_handler | plpython3_validator | $libdir/plpython3 |         |

表格字段含义如下：

| 名字            | 类型        | 描述                              |      |
| --------------- | ----------- | --------------------------------- | ---- |
| tmplname      | name     | 这个模板所应用的语言的名字        |      |
| tmpltrusted  | boolean   | 如果语言被认为是可信的，则为真    |      |
| tmplhandler   | text     | 调用处理器函数的名字              |      |
| tmplvalidator | text    | 校验函数的名字，如果没有则为 NULL |      |
| tmpllibrary  | text      | 实现语言的共享库的路径            |      |
| tmplacl      | aclitem[] | 模板的访问权限(未使用)            |      |

访问表格`pg_language`，得到结果如下：

| lanname  | lanowner | lanispl | lanpltrusted | lanplcallfoid | laninline | lanvalidator | lanacl |
|----------|----------|---------|-------------|-------------|---------|-------------|--------|
|internal |       10 | f       | f            |             0 |         0 |         2246 |
|c        |       10 | f       | f            |             0 |         0 |         2247 |
| sql      |       10 | f       | t            |             0 |         0 |         2248 |
| plpgsql  |       10 | t       | t            |         13044 |     13045 |        13046 |

#### 解决方法

经过` apt-cache search` 查找 `postgresql` 相关安装包，发现有相关安装包未安装，需要独立安装， 以`tcl`语言为例：

```shell
	sudo apt-get install postgresql-pltcl-10
	psql控制台：create language pltcl;
```
再次访问`pg_language`表格，得到：
| lanname  | lanowner | lanispl | lanpltrusted | lanplcallfoid | laninline | lanvalidator | lanacl |
| -------- | -------- | ------- | ------------ | ------------- | --------- | ------------ | ------ |
| internal | 10       | f       | f            | 0             | 0         | 2246         |        |
| c        | 10       | f       | f            | 0             | 0         | 2247         |        |
| sql      | 10       | f       | t            | 0             | 0         | 2248         |        |
| plpgsql  | 10       | t       | t            | 13044         | 13045     | 13046        |        |
| pltcl    | 10       | t       | t            | 16452         | 0         | 0            |        |


