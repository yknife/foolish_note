1. order by 全局排序，只有一个 Reducer

2. sort by 每个reduce内部排序,随机分区无规则

   ```sql
   set mapreduce.job.reduces=3;
   //查看当前reducer数量
   set mapreduce.job.reduces;
   //查看有多少个reducer（分区）
   insert overwrite local directory '/u01/ysw/sort-by' select * from emp sort by deptno desc;
   ```

3. distribute by 需要结合sort by一起使用，分组key.hashcode%分区数=分区号

   ```sql
   insert overwrite local directory '/u01/ysw/distribute-by' select * from emp distribute by deptno sort by empno ;
   ```

4. cluster by 

   ```sql
   insert overwrite local directory '/u01/ysw/distribute-by' select * from emp cluster by deptno ;
   //相当于
   insert overwrite local directory '/u01/ysw/distribute-by' select * from emp distribute by deptno sort by deptno ;
   ```

   > 当 distribute by 和 sorts by 字段相同时，可以使用 cluster by 方式。 
   >
   > cluster by 除了具有 distribute by 的功能外还兼具 sort by 的功能。但是排序只能是升序 排序，不能指定排序规则为 ASC 或者 DESC。