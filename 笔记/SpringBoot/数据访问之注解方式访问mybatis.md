```java
public interface DeptMapper {
    @Insert("insert into t_dept (id,dept_name) values (#{id},#{deptName})")
    @Options(useGeneratedKeys = true,keyProperty = "id")//返回自动生成的key，同样的功能jdbc也可实现
    void saveDept(Dept dept);
}
```