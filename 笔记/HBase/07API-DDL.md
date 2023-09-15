### 创建命名空间

```java
/**
 * 创建命名空间
 * @param namespace 命名空间名称
 */
public static void createNamespace(String namespace) throws
        IOException {
    // 1. 获取 admin
    // 此处的异常先不要抛出 等待方法写完 再统一进行处理
    // admin 的连接是轻量级的 不是线程安全的 不推荐池化或者缓存这个连接
    Admin admin = connection.getAdmin();
    // 2. 调用方法创建命名空间
    // 代码相对 shell 更加底层 所以 shell 能够实现的功能 代码一定能实现
    // 所以需要填写完整的命名空间描述
    // 2.1 创建命令空间描述建造者 => 设计师
    NamespaceDescriptor.Builder builder =
            NamespaceDescriptor.create(namespace);
    // 2.2 给命令空间添加需求
    builder.addConfiguration("user","atguigu");
    // 2.3 使用 builder 构造出对应的添加完参数的对象 完成创建
    // 创建命名空间出现的问题 都属于本方法自身的问题 不应该抛出
    try {
        admin.createNamespace(builder.build());
    } catch (IOException e) {
        System.out.println("命令空间已经存在");
        e.printStackTrace();
    }
    // 3. 关闭 admin
    admin.close();
}
```

其中builder.addConfiguration("user","atguigu")的效果，可以用以下命令查看

```sh
describe_namespace 'bigdata'
DESCRIPTION                                                                                                                            
{NAME => 'bigdata', user => 'atguigu'}                                                                                                 
Quota is disabled
Took 2.2538 seconds   
```

### 判断表格是否存在

```java
/**
 * 判断表格是否存在
 * @param namespace 命名空间名称
 * @param tableName 表格名称
 * @return ture 表示存在
 */
public static boolean isTableExists(String namespace,String
        tableName) throws IOException {
    // 1. 获取 admin
    Admin admin = connection.getAdmin();
    // 2. 使用方法判断表格是否存在
    boolean b = false;
    try {
        b = admin.tableExists(TableName.valueOf(namespace,
                tableName));
    } catch (IOException e) {
        e.printStackTrace();
    }
    // 3. 关闭 admin
    admin.close();
    // 3. 返回结果
    return b;
    // 后面的代码不能生效
}
```

### 创建表

```java
/**
 * 创建表格
 * @param namespace 命名空间名称
 * @param tableName 表格名称
 * @param columnFamilies 列族名称 可以有多个
 */
public static void createTable(String namespace , String
        tableName , String... columnFamilies) throws IOException {
    // 判断是否有至少一个列族
    if (columnFamilies.length == 0){
        System.out.println("创建表格至少有一个列族");
        return;
    }
    // 判断表格是否存在
    if (isTableExists(namespace,tableName)){
        System.out.println("表格已经存在");
        return;
    }
    // 1.获取 admin
    Admin admin = connection.getAdmin();
    // 2. 调用方法创建表格
    // 2.1 创建表格描述的建造者
    TableDescriptorBuilder tableDescriptorBuilder =
            TableDescriptorBuilder.newBuilder(TableName.valueOf(namespace,
                    tableName));
    // 2.2 添加参数
    for (String columnFamily : columnFamilies) {
        // 2.3 创建列族描述的建造者
        ColumnFamilyDescriptorBuilder
                columnFamilyDescriptorBuilder =
                ColumnFamilyDescriptorBuilder.newBuilder(Bytes.toBytes(columnFamily));
        // 2.4 对应当前的列族添加参数
        // 添加版本参数
        columnFamilyDescriptorBuilder.setMaxVersions(5);
        // 2.5 创建添加完参数的列族描述

        tableDescriptorBuilder.setColumnFamily(columnFamilyDescriptorBuilder.build());
    }
    // 2.6 创建对应的表格描述
    try {
        admin.createTable(tableDescriptorBuilder.build());
    } catch (IOException e) {
        e.printStackTrace();

    }
    // 3. 关闭 admin
    admin.close();
}
```

### 

