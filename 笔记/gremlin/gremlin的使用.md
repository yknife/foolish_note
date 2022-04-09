1. 创建图表

   ```groovy
   graph = TinkerGraph.open()
   ```

2. 创建图表的遍历

   ```groovy
   g = traversal().withEmbedded(graph)
   ```

3. 增加一个点

   ```groovy
   gremlin> v1 = g.addV("person").property(T.id, 1).property("name", "marko").property("age", 29).next()
   ==>v[1]
   gremlin> v2 = g.addV("software").property(T.id, 3).property("name", "lop").property("lang", "java").next()
   ==>v[3]
   ```

4. 增加一条边

   ```groovy
   gremlin> g.addE("created").from(v1).to(v2).property(T.id, 9).property("weight", 0.4)
   ==>e[9][1-created->3]
   ```

5. 查找一个点

   ```groovy
   g.V().has('name','marko')
   ```

6. 查找person表的marko

   ```groovy
   g.V().has('person','name','marko')
   ```

7. 查找person的created出边

   ```groovy
   g.V().has('person','name','marko').outE('created')
   ```

8. 查找person的created出边的入节点

   ```groovy
   g.V().has('person','name','marko').outE('created').inV()
   ```

9. 上面两步等同于out

   ```groovy
   g.V().has('person','name','marko').out('created')
   ```

10. 查看节点上的name属性

    ```groovy
    g.V().has('person','name','marko').out('created').values('name')
    ```

11. has中within比较器类似sql中的in

    ```groovy
    g.V().has('person','name',within('vadas','marko')).values('age')
    ```

12. 计算平均值

    ```groovy
    g.V().has('person','name',within('vadas','marko')).values('age').mean()
    ```

13. 查找与marko共同开发软件的人是谁？

    ```groovy
    g.V().has('person','name','marko').out('created').in('created').values('name')
    ```

14. 很好，我们可以看到“peter”、“josh”和“marko”都负责创建“v[3]”，也就是名为“lop”的“软件”顶点。当然，我们已经知道“marko”的参与，说“marko”与自己合作似乎很奇怪，因此将“marko”排除在结果之外似乎是合乎逻辑的。以下遍历处理该排除：

    ```groovy
    g.V().has('person','name','marko').as('exclude').
               out('created').in('created').
               where(neq('exclude')).
               values('name') 
    ```

15. 展示单步遍历步骤

    ```groovy
    g.V().as('a').out().as('b').out().as('c').
               select('a','b','c')
    ==>[a:v[1],b:v[4],c:v[5]]
    ==>[a:v[1],b:v[4],c:v[3]]
    ```

16. 按照label分组

    ```groovy
    gremlin> g.V().group().by(label)
    ==>[software:[v[3],v[5]],person:[v[1],v[2],v[4],v[6]]]
    ```

17. 按照label分组并查看节点的name属性

    ```groovy
    gremlin> g.V().group().by(label).by('name')
    ==>[software:[lop,ripple],person:[marko,vadas,josh,peter]]
    ```

    