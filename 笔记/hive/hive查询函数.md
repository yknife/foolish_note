1. 查看系统自带的函数

   ```sql
    show functions;
   ```

2. 函数说明

   ```sql
   desc function upper;
   ```

3. 扩展函数说明

   ```sql
   desc function extended upper;
   ```

4. 函数分类

   * UDF（user defined function）:一进一出。例如：upper
   * UDAF（user defined aggregation function）:多进一出。例如：min
   * UDTF（user defined Table-Generating function）:一进多出。例如：flatMap

   注意：其中“一”指的是一行。

5. 空字段赋值

   > NVL：给值为 NULL 的数据赋值，它的格式是 NVL( value，default_value)。它的功能是如 果 value 为 NULL，则 NVL 函数返回 default_value 的值，否则返回 value 的值，如果两个参数 都为 NULL ，则返回 NULL。

	```sql
	//查询：如果员工的 comm 为 NULL，则用-1 代替
	select comm,nvl(comm, -1) from emp;
	//如果员工的 comm 为 NULL，则用领导 id 代替
	select comm, nvl(comm,mgr) from emp;
	//可嵌套NVL
	select comm,nvl(nvl(comm,mgr),-1) from emp;
	```

6. CASE WHEN THEN ELSE END

   * 数据准备

   ```sql
   悟空	A	男
   大海	A	男
   宋宋	B	男
   凤姐	A	女
   婷姐	B	女
   婷婷	B	女
   ```

   * 建表

   ```sql
   create table emp_sex(name string,dept_id string,sex string) row format delimited fields terminated by '\t';
   load data local inpath '/u01/ysw/sex.txt' into table emp_sex;
   ```

   * 查询

   ```sql
   select
     dept_id,
     sum(case sex when '男' then 1 else 0 end) maleCount,
     sum(case sex when '女' then 1 else 0 end) femaleCount 
   from emp_sex 
   group by dept_id;
   ```

   * 使用if

   ```sql
   select 
     dept_id,
     sum(if(sex='男',1,0)) maleCount,
     sum(if(sex='女',1,0)) femaleCount 
   from emp_sex 
   group by dept_id;
   ```

   

   

