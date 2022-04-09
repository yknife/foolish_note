### mongo运维

* 启动mongo服务端

```shell
mongod --dbpath /usr/local/var/mongodb --logpath /usr/local/var/log/mongodb/mongo.log --fork
```





### mongo语法

* mongo数组字段合并并排重

```js
db.getCollection('person').aggregate([
    {$group:{_id:null,concat_arr:{$addToSet:'$fav'}}},
    {$addFields:{result_arr:{$reduce:{input:'$concat_arr',initialValue:[],in:{$setUnion:['$$value','$$this']}}}}}
])
```

* mongo求图形交集

```js
db.getCollection('geoTest').find({
        geo:{
            $geoIntersects:{
                $geometry:{
                        type:'Point',
                        coordinates:[10,10]
                    }
                }
            }
    })
```

