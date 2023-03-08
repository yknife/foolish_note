# Search API

基本语法

```json
GET /<index_name>/_search 
GET /_search 
```

可选参数

- size：单次查询多少条文档，默认为 10
- from：起始文档偏移量。需要为非负数，默认为0
- **timeout：**指定等待每个分片响应的时间段。如果在超时到期之前未收到响应，则请求失败并返回错误。默认为无超时。



