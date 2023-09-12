1. 写入数据

   ```sh
   put 'bigdata:student','1001','info:name','yknife'
   put 'bigdata:student','1001','info:name','wangwu'
   put 'bigdata:student','1001','info:age','10'
   get 'bigdata:student','1001'
   COLUMN                             CELL                                                                                                
    info:age                          timestamp=2023-09-08T22:24:13.636, value=10                                                         
    info:name                         timestamp=2023-09-08T22:23:54.883, value=wangwu                                                     
   1 row(s)
   ```

2. 读取一行数据

   ```sh
   get 'bigdata:student','1001'
   COLUMN                             CELL                                                                                                
    info:age                          timestamp=2023-09-08T22:24:13.636, value=10                                                         
    info:name                         timestamp=2023-09-08T22:23:54.883, value=wangwu                                                     
   1 row(s)
   # 指定列
   get 'bigdata:student', '1001', {COLUMN => 'info:age'}
   COLUMN                             CELL                                                                                                
    info:age                          timestamp=2023-09-08T22:24:13.636, value=10                                                         
   1 row(s)
   # 指定多列
   get 'bigdata:student', '1001', {COLUMN => ['info:name', 'info:age']}
   COLUMN                             CELL                                                                                                
    info:age                          timestamp=2023-09-08T22:24:13.636, value=10                                                         
    info:name                         timestamp=2023-09-08T22:23:54.883, value=wangwu                                                     
   1 row(s)
   # 查看字段最新的5个版本，版本数上限为字段本身的VERSIONS，即使命令中VERSIONS大于字段本身
   get 'bigdata:student','1001',{COLUMNS=>['info:name'],VERSIONS=>5}
   COLUMN                             CELL                                                                                                
    info:name                         timestamp=2023-09-08T22:54:38.693, value=333                                                        
    info:name                         timestamp=2023-09-08T22:54:34.901, value=222                                                        
    info:name                         timestamp=2023-09-08T22:54:30.421, value=111                                                        
    info:name                         timestamp=2023-09-08T22:54:08.508, value=lilei                                                      
    info:name                         timestamp=2023-09-08T22:51:58.192, value=yknife                                                     
   1 row(s)
   ```

3. 读取多行数据

   ```sh
   # scan 是扫描数据，能够读取多行数据，不建议扫描过多的数据，推荐使用 startRow 和stopRow 来控制读取的数据，默认范围左闭右开。
   scan 'bigdata:student'
   ROW                                COLUMN+CELL                                                                                         
    1001                              column=info:age, timestamp=2023-09-08T22:24:13.636, value=10                                        
    1001                              column=info:name, timestamp=2023-09-08T22:23:54.883, value=wangwu                                   
    1002                              column=info:age, timestamp=2023-09-08T22:24:56.246, value=10                                        
   2 row(s)
   scan 'bigdata:student',{STARTROW=>'1001',STOPROW=>'1002'}
   ROW                                COLUMN+CELL                                                                                         
    1001                              column=info:age, timestamp=2023-09-08T22:24:13.636, value=10                                        
    1001                              column=info:name, timestamp=2023-09-08T22:23:54.883, value=wangwu                                   
   1 row(s)
   scan 'bigdata:student',{COLUMNS => ['info:name'],STARTROW=>'1001',LIMIT=>10}
   ROW                                COLUMN+CELL                                                                                         
    1001                              column=info:name, timestamp=2023-09-08T22:23:54.883, value=wangwu                                   
   1 row(s)
   scan 'bigdata:student',{COLUMNS => ['info:age'],STARTROW=>'1001',LIMIT=>10}
   ROW                                COLUMN+CELL                                                                                         
    1001                              column=info:age, timestamp=2023-09-08T22:24:13.636, value=10                                        
    1002                              column=info:age, timestamp=2023-09-08T22:24:56.246, value=10                                        
   2 row(s)
   ```

4. 删除数据

   ```sh
   # delete 表示删除一个版本的数据，即为 1 个 cell，不填写版本默认删除最新的一个版本。
   delete 'bigdata:student','1001','info:name'
   
   #deleteall 表示删除所有版本的数据，即为当前行当前列的多个 cell。（执行命令会标记数据为要删除，不会直接将数据彻底删除，删除数据只在特定时期清理磁盘时进行）
   deleteall 'bigdata:student','1001','info:name'
   ```

   

   

