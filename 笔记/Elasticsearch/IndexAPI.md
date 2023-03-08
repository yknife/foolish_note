# Index API

## 3.1 Settings：索引设置（见：第四章 3.2 小节）

**基本语法**

```json
PUT <index_name>
```

### 3.1.1 设置 Settings

**创建索引的时候指定 settings**

```json
PUT <index_name>
{
  "settings": {}
}
```

**例如**

创建索引`test_setting`，指定其主分片数量为 1，每个主分片的副本数量为 1。

```json
PUT test_setting
{
  "settings": {
    "number_of_shards": 1,  //主分片数量为 1
    "number_of_replicas": 1 //每个主分片分配一个副本
  }
}
```

### 3.1.2 修改 settings

使用 _setting 只能修改允许动态修改的配置项

**语法**

```json
PUT /<index_name>/_settings
```

**例如**

修改索引`test_setting`索引，把每个主分片的副本数量修改为 0。

```json
PUT test_setting/_settings
{
  "number_of_shards": 0
}
```

### 3.1.3 静态索引设置

只能在创建索引时或在关闭状态的索引上设置。

**重要的静态配置**

- **index.number_of_shards**：索引的主分片的个数，默认为 1，此设置只能在创建索引时设置

每个索引的分片的数量上限为 1024，这是一个安全限制，以防止意外创建索引，这些索引可能因资源分配而破坏集群的稳定性。export ES_JAVA_OPTS=“-Des.index.max_number_of_shards=128” 可以通过在属于集群的每个节点上指定系统属性来修改限制

### 3.1.4 动态索引设置

即可以使用 _setting API 在实时修改的配置项。

**重要的动态配置**

- **index.number_of_replicas：**每个主分片的副本数。默认为 1，允许配置为 0。
- **index.refresh_interval：**执行刷新操作的频率，默认为1s. 可以设置 -1 为禁用刷新。
- **index.max_result_window：**from + size搜索此索引 的最大值。默认为 10000. 搜索请求占用堆内存和时间 from + size，这限制了内存。请参阅 [Scroll](https://www.elastic.co/guide/en/elasticsearch/reference/8.5/paginate-search-results.html#scroll-search-results)或[Search After](https://www.elastic.co/guide/en/elasticsearch/reference/8.5/paginate-search-results.html#search-after)以获得更有效的替代方法。

**推荐阅读**：[增大 max_result_window 是错的，ES只能查询前10000条数据的正确解决方案](http://www.elastic.org.cn/archives/maxresultwindow)

## 3.2 创建索引

### 3.2.1 基本语法

```json
PUT <index_name>
```

- index_name：索引名称

### 3.2.2 索引命名规范

- 必须全部小写
- 索引名称中不能包含以下符号：`\`、`/`、`*`、`?`、`"`、`<`、`>`、`|`、` `空白符号、`,`、`#`
- 7.0 之前的索引可以包含冒号英文冒号 : ，但在 7.x 及之后版本中不再受支持。
- 不使用中文命名索引
- 可以使用`-`、`_`或者`+`三种符号，但是不能以这三种符号开头。
- 不能是`.`或者`..`
- 不能超过 255 个字节长度
- 业务索引不能以`.`开头，因为这些索引是给`内部索引（如数据流中的后备索引）`和`隐藏索引`使用的

**总结**：如过记不住这些规则，请牢记以下几点

- 以小写英文字母命名索引
- 不要使用`驼峰`或者`帕斯卡`命名法则
- 如过出现多个单词的索引名称，以全小写 + 下划线分隔的方式：如`test_index`。

### 3.2.3 创建

## 3.3 删除索引

### 3.3.1 基本语法

```json
DELETE /<index_name>
```

- index_name：索引名称

## 3.4 判断索引是否存在

```json
HEAD <index_name>
```

## 3.5 索引的不可变性

ES 索引创建成功之后，以下属性将不可修改

- 索引名称
- 主分片数量
- 字段类型

## 3.6 Reindex