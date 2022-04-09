1. "Modern" 图的构成

   <img src="/Users/yknife/Documents/笔记/截图/tinkerpop-modern.png" alt="tinkerpop modern" style="zoom: 67%;" />

   2. 创建图对象和遍历对象

      ```groovy
      gremlin> graph = TinkerFactory.createModern()
      ==>tinkergraph[vertices:6 edges:6]
      gremlin> g = traversal().withEmbedded(graph)
      ==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
      ```

   3. 常用的遍历图的方法

      ```groovy
      gremlin> g.V() //// (1)
      ==>v[1]
      ==>v[2]
      ==>v[3]
      ==>v[4]
      ==>v[5]
      ==>v[6]
      gremlin> g.V(1) //// (2)
      ==>v[1]
      gremlin> g.V(1).values('name') //// (3)
      ==>marko
      gremlin> g.V(1).outE('knows') //// (4)
      ==>e[7][1-knows->2]
      ==>e[8][1-knows->4]
      gremlin> g.V(1).outE('knows').inV().values('name') //// (5)
      ==>vadas
      ==>josh
      gremlin> g.V(1).out('knows').values('name') //// (6)
      ==>vadas
      ==>josh
      gremlin> g.V(1).out('knows').has('age', gt(30)).values('name') //// (7)
      ==>josh
      ```

      