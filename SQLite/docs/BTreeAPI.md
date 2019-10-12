# SQLite BTree API

*SQLite是模块化设计，模块与模块之间的耦合度低，为此，在设计时，btree的每个功能都使用接口封装，在上层VDBE层调用时，调用相关接口，以降低模块耦合性，以下分类整理为个人思路，部分可以通过名称理解的未作标注，BTree API作用来自官网文档、代码注释翻译及个人理解*



| 类型                      | 作用                                                         | 数量 | 举例                                                         |
| ------------------------- | ------------------------------------------------------------ | ---- | ------------------------------------------------------------ |
| 存取/事务                 | 建立数据库连接，处理事务相关的开始和提交                     | 18   | Sqlite3BTreeopen() Sqlite3BTreeclose() Sqlite3BTreebegintrans() |
| 表                        | 针对表的操作，表的建立删除等                                 | 4    | Sqlite3BTreecreatetable() Sqlite3BTreedroptable()            |
| 游标相关                  | 控制数据库游标，使用游标读取数据，查找记录                   | 26   | Sqlite3BTreecursor() Sqlite3BTreeclosecursor()               |
| DML                       | 数据库查找删除操作                                           | 2    | Sqlite3BTreedelete() Sqlite3BTreeinsert()                    |
| page/cache 控制与配置函数 | 用于配置底层页面和缓存的相关参数，如页面大小，定义payload等  | 28   | sqlite3BtreePayload() Sqlite3BTreesetpagesize()              |
| 互斥锁结构                | 使用共享缓存执行多线程，使用互斥结构体时，编译时使用SQLITE_OMIT_SHARED_CACHE字段来定义 | 11   | sqlite3BtreeEnter() sqlite3BtreeLeave()                      |

### 存取/事务

Sqlite3BTreeopen

Sqlite3BTreeclose

Sqlite3BTreebegintrans

Sqlite3BTreecommit

Sqlite3BTreerollback 

Sqlite3BTreebeginstmt：开始语句事务

sqlite3BtreeCommitPhaseOne

sqlite3BtreeCommitPhaseTwo

sqlite3BtreeIsInTrans

sqlite3BtreeIsInReadTrans： 判断事务是否为active

sqlite3BtreeIsInBackup

sqlite3BtreeSavepoint

sqlite3BtreeGetFilename

sqlite3BtreeGetJournalname

sqlite3BtreeCopyFile

sqlite3BtreeGetMeta

sqlite3BtreeUpdateMeta

sqlite3BtreeNewDb

### 表相关

Sqlite3BTreecreatetable：新建一个新的btree

Sqlite3BTreedroptable

Sqlite3BTreecleartable：删除数据但保持树的结构完整

sqlite3BtreeLockTable

### 游标相关

Sqlite3BTreecursor：创建游标

Sqlite3BTreeclosecursor

sqlite3BtreeClearCursor

Sqlite3BTreefirst ：第一条记录

Sqlite3BTreelast

Sqlite3BTreenext

sqlite3BtreeEof

sqlite3BtreeIntegerKey

sqlite3BtreeOffset

Sqlite3BTreeprevious

sqlite3BtreeClearTableOfCursor

sqlite3BtreeTripAllCursors

sqlite3BtreeFakeValidCursor

sqlite3BtreeCursorSize

sqlite3BtreeCursorZero：这里的简单方法是将整个对象memset()设置为零。但是，apPage[]和aiIdx[]数组不需要为零，而且它们很大，所以我们可以跳过这些元素的初始化，从而节省大量运行时间。

sqlite3BtreeCursorHintFlags

sqlite3BtreeCursorHint

sqlite3BtreeMovetoUnpacked

sqlite3BtreeCursorHasMoved

sqlite3BtreeCursorRestore

sqlite3BtreeRowCountEst

sqlite3BtreeIncrblobCursor

sqlite3BtreeCursorHasHint

sqlite3BtreeCursorInfo：SQLITE_TEST

sqlite3BtreeCursorList：SQLITE_TEST

sqlite3BtreeCheckpoint：SQLITE_OMIT_WAL

### DML

Sqlite3BTreedelete

Sqlite3BTreeinsert

### page/cache 控制与配置函数

Sqlite3BTreesetcachesize

sqlite3BtreeSetSpillSize：mxPage，查询当前溢出大小

Sqlite3BTreesetpagesize

Sqlite3BTreegetpagesize

Sqlite3BTreesetautovacuum： 设置自动清理空闲页属性

Sqlite3BTreegetautovacuum：获取是否自动清理页

sqlite3BtreeIncrVacuum ：执行一个单位的工作，以达到增量真空

sqlite3BtreeSetPagerFlags

sqlite3BtreeLastPage ：更改BTS_SECURE_DELETE和BTS_OVERWRITE标志的值

sqlite3BtreeMaxPageCount

sqlite3BtreeGetOptimalReserve、

sqlite3BtreeGetReserveNoMutex

sqlite3BtreeSchema ：此函数返回指向与单个共享btree关联的内存块的指针

sqlite3BtreeSchemaLocked

sqlite3BtreeSetMmapLimit

sqlite3BtreePayload

sqlite3BtreePayloadFetch

sqlite3BtreePayloadSize

sqlite3BtreeMaxRecordSize

sqlite3BtreeIntegrityCheck

sqlite3BtreePayloadChecked

sqlite3BtreeSetVersion

sqlite3BtreePutData

sqlite3BtreeIsReadonly

sqlite3HeaderSizeBtree

sqlite3BtreeCursorIsValid    

sqlite3BtreeCursorIsValidNN

sqlite3BtreeCount

### 互斥锁结构

sqlite3BtreeEnter

sqlite3BtreeEnterAll

sqlite3BtreeSharable

sqlite3BtreeEnterCursor

sqlite3BtreeConnectionCount

sqlite3BtreeLeave

sqlite3BtreeLeaveCursor

sqlite3BtreeLeaveAll

sqlite3BtreeHoldsMutex

sqlite3BtreeHoldsAllMutexes

sqlite3SchemaMutexHeld