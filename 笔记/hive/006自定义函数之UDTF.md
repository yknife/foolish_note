### 编写Java代码

```java
public class MyWordCount extends GenericUDTF {

    List<String> outputList = new ArrayList<>();

    @Override
    public StructObjectInspector initialize(StructObjectInspector argOIs) throws UDFArgumentException {
        List<String> fieldNames = new ArrayList<>();
        fieldNames.add("word");
        List<ObjectInspector> fieldOIs =new ArrayList<>();
        fieldOIs.add(PrimitiveObjectInspectorFactory.javaStringObjectInspector);
        return
                ObjectInspectorFactory.getStandardStructObjectInspector(fieldNames,
                        fieldOIs);

    }

    @Override
    public void process(Object[] objects) throws HiveException {
        String line = objects[0].toString();
        String splitStr = objects[1].toString();
        String[] split = line.split(splitStr);
        for(String str:split){
            outputList.clear();
            outputList.add(str);
            forward(outputList);
        }
    }

    @Override
    public void close() throws HiveException {

    }
}
```

### 添加jar

```sql
add jar /u01/ysw/helloHive-1.0-SNAPSHOT.jar
```

### 创建UDF

```sql
create temporary function my_wc as "org.example.hive.MyWordCount"
```

### 使用

```sql
select my_wc('hello,world,java,hive',',');
```

### 准备数据

```sql
hello world
hello java
hello hive
```

```sql
create table word_count (line string) row format delimited fields terminated by "\t";
load data local inpath '/u01/ysw/udtf_wc.txt' into table word_count;
```

