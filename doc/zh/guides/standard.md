# **SQL 开发规范及基本原则**

## **SQL 开发规范及基本原则**

本文主要介绍在开发业务及应用时，需要遵守的 SQL 规范及基本原则。

**对象命名规范**

•标识符以 Unicode 字母或下划线()开头,后续字符可以使用 Unicode 字母，下划线()，数字(0-9)或者美元符号(\$)；

• 标识符建议使用具有意义的词汇，在多个词汇中间使用下划线分隔；

• 不允许用 ZNBase 的关键字如 group，order 等作为标识符，除非关键字 SQL 元素的语法接受该关键字，例如 name 接受非预留或列名关键字

• 一个数据库中的每个表名称必须是唯一的

• 建议所有数据库对象使用小写字母

**创建、删除表规范**

基本原则：表的建立在遵循表命名规范的前提下，用户在创建和删除表时应该增加相应的逻辑判断，以防止业务流程异常中断。例如：`createtable if not exists table_name` 或者 `drop table if exists table_name`，使用 if 的条件判断；尽量使用多行插入而非多次的单行插入。

**`SELECT *` 使用规范**

基本原则：SELECT 查询应按照需求选取合适的字段，并尽可能减少使用 `SELECT *`进行查询。这样可以尽量减少网络带宽资源的消耗，提高索引的使用效率。

**事务规范**

ZNBase 默认单行数据不大于 64MB，ZNBase 中，bit 等类型可以存储任意的大小，但为性能考虑，建议记录保持在 1MB 以下保证性能。超过该阈值，写放大和其他因素可能导致数据库性能的显著下降。

| 注意：                                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 在 ZNBase 中，数据的读取和写入会在数据库中进行多次的流通，因此存在读放大和写放大的情况，当涉及到较大量的数据时，会导致内存占用的大幅上升，在进行大事务前进行相应的性能评估。 |

**字段上函数使用规范**

基本原则：可以在表的字段上使用相关函数，但是不建议在 Where 条件中的过滤条件中字段上使用任何函数。这种形式会在性能上有所损失。

**数据删除规范**

基本原则：当删除表中全部数据的时候，使用 TRUNCATE 或者 DROP 后重建，而非使用 DELETE 清空表。

详细说明：TRUNCATE 会通过删除掉表并重新创建完全相同的表，而 DELETE 会执行多个事务一行一行地清空表，在性能上 TRUNCATE 更高。

**其他规范**

WHERE 条件中不在索引列上进行数学运算或函数运算。