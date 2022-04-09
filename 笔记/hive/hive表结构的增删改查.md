1. 基本数据类型

   ![image-20220220213501042](/Users/yknife/Documents/笔记/截图/image-20220220213501042.png)

​		对于 Hive 的 String 类型相当于数据库的 varchar 类型，该类型是一个可变的字符串，不 过它不能声明其中最多能存储多少个字符，理论上它可以存储 2GB 的字符数。

2. 集合数据类型

   ![image-20220220213706071](/Users/yknife/Documents/笔记/截图/image-20220220213706071.png)

​	Hive 有三种复杂数据类型 ARRAY、MAP 和 STRUCT。ARRAY 和 MAP 与 Java 中的 Array 和 Map 类似，而 STRUCT 与 C 语言中的 Struct 类似，它封装了一个命名字段集合，复杂数据 类型允许任意层次的嵌套。

3. 实操

   ```json
   {
    "name": "songsong",
    "friends": ["bingbing" , "lili"] , //列表 Array,
    "children": { //键值 Map,
    "xiao song": 18 ,
    "xiaoxiao song": 19
    }
    "address": { //结构 Struct,
    "street": "hui long guan",
    "city": "beijing"
    }
   }
   ```

   * 创建本地数据

   ```sh
   songsong,bingbing_lili,xiao song:18_xiaoxiao song:19,hui long guan_beijing
   yangyang,caicai_susu,xiao yang:18_xiaoxiao yang:19,chao yang_beijing
   ```

   * Hive 上创建测试表 test

   ```sql
   create table test1(
   name string,
   friends array<string>,
   children map<string, int>,
   address struct<street:string, city:string>
   )
   row format delimited fields terminated by ','
   collection items terminated by '_'
   map keys terminated by ':'
   lines terminated by '\n';
   ```

4. 创建数据库

   ```sql
   CREATE DATABASE [IF NOT EXISTS] database_name
   [COMMENT database_comment]
   [LOCATION hdfs_path]
   [WITH DBPROPERTIES (property_name=property_value, ...)];
   ```

5. 创建一个数据库，指定数据库在 HDFS 上存放的位置

   ```sql
    create database db_hive2 location '/db_hive2.db';
   ```

6. 显示数据库详情

   ```sql
   desc database db_hive;
   ```

7. 删除数据库

   ```sql
   drop database db_hive2;
   drop database db_hive cascade;
   ```

8. 创建表

   ```sql
   CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name
   [(col_name data_type [COMMENT col_comment], ...)]
   [COMMENT table_comment]
   [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
   [CLUSTERED BY (col_name, col_name, ...)
   [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
   [ROW FORMAT row_format]
   [STORED AS file_format]
   [LOCATION hdfs_path]
   [TBLPROPERTIES (property_name=property_value, ...)]
   [AS select_statement]
   ```

   1. CREATE TABLE 创建一个指定名字的表。如果相同名字的表已经存在，则抛出异常； 用户可以用 IF NOT EXISTS 选项来忽略这个异常。

   2. EXTERNAL 关键字可以让用户创建一个外部表，在建表的同时可以指定一个指向实 际数据的路径（LOCATION），在删除表的时候，内部表的元数据和数据会被一起删除，而外 部表只删除元数据，不删除数据。

   3. COMMENT：为表和列添加注释。

   4. PARTITIONED BY 创建分区表

   5. CLUSTERED BY 创建分桶表

   6. SORTED BY 不常用，对桶中的一个或多个列另外排序

   7. 分隔符

      ROW FORMAT DELIMITED [FIELDS TERMINATED BY char] 

      [COLLECTION ITEMS TERMINATED BY char] 

      [MAP KEYS TERMINATED BY char]

       [LINES TERMINATED BY char] 

      | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)] 

      用户在建表的时候可以自定义 SerDe 或者使用自带的 SerDe。如果没有指定 ROW FORMAT 或者 ROW FORMAT DELIMITED，将会使用自带的 SerDe。在建表的时候，用户还需 要为表指定列，用户在指定表的列的同时也会指定自定义的 SerDe，Hive 通过 SerDe 确定表 的具体的列的数据。 SerDe 是 Serialize/Deserilize 的简称， hive 使用 Serde 进行行对象的序列与反序列化。

   8. STORED AS 指定存储文件类型

      常用的存储文件类型：SEQUENCEFILE（二进制序列文件）、TEXTFILE（文本）、RCFILE（列 式存储格式文件）

      如果文件数据是纯文本，可以使用STORED AS TEXTFILE。如果数据需要压缩，使用 STORED AS SEQUENCEFILE。

   9. LOCATION ：指定表在 HDFS 上的存储位置。

   10. AS：后跟查询语句，根据查询结果创建表。

   11. LIKE 允许用户复制现有的表结构，但是不复制数据。

9. 外部表

   > 因为表是外部表，所以 Hive 并非认为其完全拥有这份数据。删除该表并不会删除掉这 份数据，不过描述表的元数据信息会被删除掉.
   >
   > ```sql
   > create external table if not exists dept(
   > deptno int,
   > dname string,
   > loc int
   > )
   > row format delimited fields terminated by '\t';
   > desc formatted dept;
   > //内部表对应MANAGED_TABLE，外部表对应EXTERNAL_TABLE
   > //内外部表互相转换
   > alter table student2 set tblproperties('EXTERNAL'='TRUE');
   > alter table dept set serdeproperties('field.delim'=' ');
   > ```

10. 修改表

    * 修改表名

      ```sql
      ALTER TABLE table_name RENAME TO new_table_name
      ```

    * 增加/修改/替换列信息

      ```sql
      -- （1）更新列
      ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name
      column_type [COMMENT col_comment] [FIRST|AFTER column_name]
      -- （2）增加和替换列
      ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type [COMMENT
      col_comment], ...) 
      -- 注：ADD 是代表新增一字段，字段位置在所有列后面(partition 列前)，REPLACE 则是表示替换表中所有字段。
      ```

    * 例子

      ```sql
      alter table dept add columns(deptdesc string);
      alter table dept change column deptdesc desc string;
      alter table dept replace columns(deptno string, dname string, loc string);
      ```

    * 删除表

      ```sql
      drop table dept;
      ```

11. DML数据操作

    ```sql
    load data [local] inpath '数据的 path' [overwrite] into table student [partition (partcol1=val1,…)];
    ```

    * load data:表示加载数据
    * local:表示从本地加载数据到 hive 表；否则从 HDFS 加载数据到 hive 表
    * inpath:表示加载数据的路径
    * overwrite:表示覆盖表中已有数据，否则表示追加
    * into table:表示加载到哪张表
    * student:表示具体的表
    * partition:表示上传到指定分区

    1. 造数据student.txt

    ```sh
    1002	yknife
    1003	leo
    1004	lily
    ```

    2. 创建student表

    ```sql
    create table if not exists student (id int,name string) row format delimited fields terminated by '\t';
    ```

    3. 本地文件加载数据

    ```sql
    load data local inpath '/opt/module/hive/datas/student.txt' into table default.student;
    ```

    4. 加载 HDFS 文件到 hive 中

    ```sql
    dfs -put /opt/module/hive/data/student.txt /user/atguigu/hive;
    load data inpath '/user/atguigu/hive/student.txt' into table default.student;
    ```

    5. 覆盖已有数据

    ```sql
     load data inpath '/user/atguigu/hive/student.txt' overwrite into table default.student;
    ```

12. 清空表数据,只能删除管理表，不能删除外部表中数据

    ```sql
    truncate table student;
    ```

    

