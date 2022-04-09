1. fold()的功能是将多行记录转换为一个list

   ```groovy
   gremlin> g.V(1).out('knows').values('name')
   ==>vadas
   ==>josh
   gremlin> g.V(1).out('knows').values('name').fold()
   ==>[vadas,josh]
   ```

2. `repeat()` (**branch**) 单步用于按退出条件（predicate）来循环执行遍历。下面是使用 `repeat()` 的例子

   ```groovy
   g.V(1).repeat(out()).times(2).path().by('name') //1
   g.V().until(has('name','ripple')).
         repeat(out()).path().by('name') //2
   ```

   1. do-while 语义，连续执行 `out` 两次
   2. while-do 语义，如果遍历器遇到顶点name是ripple就退出

3. Inject(),“可插入单步”的理念其实是让“能够任意的在遍历流中插入对象”变得可能。总体来说 `inject()` (**sideEffect**) 单步是存在的，下面是一些例子

   ![inject step](/Users/yknife/Documents/笔记/截图/inject-step.png)

   ```groovy
   g.V(4).out().values('name').inject('daniel')
   g.V(4).out().values('name').inject('daniel').map {it.get().length()}
   g.V(4).out().values('name').inject('daniel').map {it.get().length()}.path()
   ```

   