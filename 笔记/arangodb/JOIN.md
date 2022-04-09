多对一

1. 用户与城市的关系，准备数据

   ```js
   insert {
     name : 'yknife'
   } into users
   //users/13572
   ```

   ```js
   insert {
     name : 'Amoy'
   } into citys
   ```

   ```js
   update "13572" 
   with {
     city : "citys/13655"
   } in users
   ```

2. 关联查询

   ```js
   for u in users
    for c in citys
       filter u.city == c._id
       return {
           user : u,
           city : c
       }
   ```

   输出结果：为关联的的两个对象，没有嵌套关系

   ```json
   [
     {
       "user": {
         "_key": "13572",
         "_id": "users/13572",
         "_rev": "_d9mGMuS---",
         "name": "yknife",
         "city": "citys/13655"
       },
       "city": {
         "_key": "13655",
         "_id": "citys/13655",
         "_rev": "_d9lkbJ----",
         "name": "Amoy"
       }
     }
   ]
   ```

3. 关联查询（嵌套）

   ```json
   for u in users
    for c in citys
       filter u.city == c._id
       return merge(u,{city:c})
   ```

   输出结果

   ```json
   [
     {
       "_id": "users/13572",
       "_key": "13572",
       "_rev": "_d9mGMuS---",
       "city": {
         "_key": "13655",
         "_id": "citys/13655",
         "_rev": "_d9lkbJ----",
         "name": "Amoy"
       },
       "name": "yknife"
     }
   ]
   ```

4. 