<img src="/Users/yknife/Library/Application Support/typora-user-images/image-20220320174721776.png" alt="image-20220320174721776" style="zoom:50%;" />

1. [参考](http://sql2gremlin.com/)

2. 启动gremlin

   ```sh
   /bin/gremlin.sh -i /tmp/northwind.groovy
   ```

3. 加载北风图

   ```groovy
   gremlin> graph = NorthwindFactory.createGraph()
   ==>tinkergraph[vertices:3209 edges:6177]
   gremlin> g = graph.traversal()
   ==>graphtraversalsource[tinkergraph[vertices:3209 edges:6177], standard]
   ```

4. Select calculated column 查询计算列

   **SQL**

   ```sql
   SELECT LEN(CategoryName)
     FROM Categories
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().hasLabel("category").values("name").
                  map {it.get().length()}
   ==>9
   ==>10
   ==>11
   ==>14
   ==>14
   ==>12
   ==>7
   ==>7
   ```

5. ### Select distinct values 查询排重列

   **SQL**

   ```sql
   SELECT DISTINCT LEN(CategoryName)
     FROM Categories
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().hasLabel("category").values("name").
                  map {it.get().length()}.dedup()
   ==>9
   ==>10
   ==>11
   ==>14
   ==>12
   ==>7
   ```

6. ### Select scalar value 查询最大值

   **SQL**

   ```sql
   SELECT MAX(LEN(CategoryName))
     FROM Categories
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().hasLabel("category").values("name").
                  map {it.get().length()}.max()
   ==>14
   ```

7. 查询没有库存的产品

   **SQL**

   ```sql
   SELECT ProductName, UnitsInStock
     FROM Products
    WHERE UnitsInStock = 0
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().has("product", "unitsInStock", 0).valueMap("name", "unitsInStock")
   ==>[unitsInStock:[0],name:[Chef Anton's Gumbo Mix]]
   ==>[unitsInStock:[0],name:[Alice Mutton]]
   ==>[unitsInStock:[0],name:[Thüringer Rostbratwurst]]
   ==>[unitsInStock:[0],name:[Gorgonzola Telino]]
   ==>[unitsInStock:[0],name:[Perth Pasties]]
   ```

8. 查询有库存的商品

   **SQL**

   ```sql
   SELECT ProductName, UnitsOnOrder
     FROM Products
    WHERE NOT(UnitsOnOrder = 0)
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().has("product", "unitsOnOrder", neq(0)).
                  valueMap("name", "unitsOnOrder")
   ==>[name:[Chang],unitsOnOrder:[40]]
   ==>[name:[Aniseed Syrup],unitsOnOrder:[70]]
   ==>[name:[Queso Cabrales],unitsOnOrder:[30]]
   ==>[name:[Sir Rodney's Scones],unitsOnOrder:[40]]
   ==>[name:[Gorgonzola Telino],unitsOnOrder:[70]]
   ==>[name:[Mascarpone Fabioli],unitsOnOrder:[40]]
   ==>[name:[Gravad lax],unitsOnOrder:[50]]
   ==>[name:[Ipoh Coffee],unitsOnOrder:[10]]
   ==>[name:[Rogede sild],unitsOnOrder:[70]]
   ==>[name:[Chocolade],unitsOnOrder:[70]]
   ...
   ```

9. 查询在价格区间中的商品

   **SQL**

   ```sql
   SELECT ProductName, UnitPrice
     FROM Products
    WHERE UnitPrice >= 5 AND UnitPrice < 10
   ```

   **Gremlin**

   ```groovy
   gremlin> g.V().has("product", "unitPrice", between(5f, 10f)).
                  valueMap("name", "unitPrice")
   ==>[unitPrice:[6.0],name:[Konbu]]
   ==>[unitPrice:[9.2],name:[Teatime Chocolate Biscuits]]
   ==>[unitPrice:[9.0],name:[Tunnbröd]]
   ==>[unitPrice:[9.65],name:[Jack's New England Clam Chowder]]
   ==>[unitPrice:[9.5],name:[Rogede sild]]
   ==>[unitPrice:[9.5],name:[Zaanse koeken]]
   ==>[unitPrice:[7.0],name:[Filo Mix]]
   ==>[unitPrice:[7.45],name:[Tourtière]]
   ==>[unitPrice:[7.75],name:[Rhönbräu Klosterbier]]
   ```

10. 多条件查询

    **SQL**

    ```sql
    SELECT ProductName, UnitsInStock
      FROM Products
     WHERE Discontinued = 1
       AND UnitsInStock <> 0
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().has("product", "discontinued", true).has("unitsInStock", neq(0)).
                  valueMap("name", "unitsInStock")
    ==>[unitsInStock:[29],name:[Mishi Kobe Niku]]
    ==>[unitsInStock:[20],name:[Guaraná Fantástica]]
    ==>[unitsInStock:[26],name:[Rössle Sauerkraut]]
    ==>[unitsInStock:[26],name:[Singaporean Hokkien Fried Mee]]
    ```

11. 按照商品的价格排序

    **SQL**

    ```sql
      SELECT ProductName, UnitPrice
        FROM Products
    ORDER BY UnitPrice ASC
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("product").order().by("unitPrice", asc).
                   valueMap("name", "unitPrice")
    ==>[unitPrice:[2.5],name:[Geitost]]
    ==>[unitPrice:[4.5],name:[Guaraná Fantástica]]
    ==>[unitPrice:[6.0],name:[Konbu]]
    ==>[unitPrice:[7.0],name:[Filo Mix]]
    ==>[unitPrice:[7.45],name:[Tourtière]]
    ==>[unitPrice:[7.75],name:[Rhönbräu Klosterbier]]
    ==>[unitPrice:[9.0],name:[Tunnbröd]]
    ==>[unitPrice:[9.2],name:[Teatime Chocolate Biscuits]]
    ==>[unitPrice:[9.5],name:[Rogede sild]]
    ==>[unitPrice:[9.5],name:[Zaanse koeken]]
    ...
    ```

    [order](https://tinkerpop.apache.org/docs/current/reference/#order-step)

12. 查询指定条数的数据

    **SQL**

    ```sql
      SELECT TOP (5) ProductName, UnitPrice
        FROM Products
    ORDER BY UnitPrice
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("product").order().by("unitPrice", incr).limit(5).
                   valueMap("name", "unitPrice")
    ==>[unitPrice:[2.5],name:[Geitost]]
    ==>[unitPrice:[4.5],name:[Guaraná Fantástica]]
    ==>[unitPrice:[6.0],name:[Konbu]]
    ==>[unitPrice:[7.0],name:[Filo Mix]]
    ==>[unitPrice:[7.45],name:[Tourtière]]
    ```

13. 分页查询数据

    **SQL**

    ```sql
       SELECT Products.ProductName, Products.UnitPrice
         FROM (SELECT ROW_NUMBER()
                        OVER (
                          ORDER BY UnitPrice) AS [ROW_NUMBER],
                      ProductID
                 FROM Products) AS SortedProducts
           INNER JOIN Products
                   ON Products.ProductID = SortedProducts.ProductID
        WHERE [ROW_NUMBER] BETWEEN 6 AND 10
     ORDER BY [ROW_NUMBER]
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("product").order().by("unitPrice", incr).range(5, 10).
                   valueMap("name", "unitPrice")
    ==>[unitPrice:[7.75],name:[Rhönbräu Klosterbier]]
    ==>[unitPrice:[9.0],name:[Tunnbröd]]
    ==>[unitPrice:[9.2],name:[Teatime Chocolate Biscuits]]
    ==>[unitPrice:[9.5],name:[Rogede sild]]
    ==>[unitPrice:[9.5],name:[Zaanse koeken]]
    ```

14. 使用商品价格分组，并找到最常用的商品价格

    **SQL**

    ```sql
      SELECT TOP(1) UnitPrice
        FROM (SELECT Products.UnitPrice,
                     COUNT(*) AS [Count]
                FROM Products
            GROUP BY Products.UnitPrice) AS T
    ORDER BY [Count] DESC
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("product").groupCount().by("unitPrice").
                   order(local).by(values, decr).select(keys).limit(local, 1)
    ==>18.0
    ```

    `order(local)` 排序的也是当前的局部变量，而不是遍历流中的对象。 这对于 `Collection` 和 `Map` 类型对象生效。遇到其他类型对象就原样地被返回。

15. 查找指定分类名的商品，类似sql中的join

    **SQL**

    ```sql
        SELECT Products.ProductName
          FROM Products
    INNER JOIN Categories
            ON Categories.CategoryID = Products.CategoryID
         WHERE Categories.CategoryName = 'Beverages'
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().has("name","Beverages").in("inCategory").values("name")
    ==>Chai
    ==>Rhönbräu Klosterbier
    ==>Chartreuse verte
    ==>Chang
    ==>Lakkalikööri
    ==>Ipoh Coffee
    ==>Guaraná Fantástica
    ==>Côte de Blaye
    ==>Steeleye Stout
    ==>Outback Lager
    ...
    ```

16. 统计所有用户的订单数

    **SQL**

    ```sql
        SELECT Customers.CustomerID, COUNT(Orders.OrderID)
          FROM Customers
     LEFT JOIN Orders
            ON Orders.CustomerID = Customers.CustomerID
      GROUP BY Customers.CustomerID
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("customer").match(
               __.as("c").values("customerId").as("customerId"),
               __.as("c").out("ordered").count().as("orders")
             ).select("customerId", "orders")
    ==>[customerId:ALFKI,orders:6]
    ==>[customerId:ANATR,orders:4]
    ==>[customerId:ANTON,orders:7]
    ==>[customerId:AROUT,orders:13]
    ==>[customerId:BERGS,orders:18]
    ==>[customerId:BLAUS,orders:7]
    ==>[customerId:BLONP,orders:11]
    ==>[customerId:BOLID,orders:3]
    ==>[customerId:BONAP,orders:17]
    ==>[customerId:BOTTM,orders:14]
    ...
    ```

17. 连接，union

    **SQL**

    ```sql
    SELECT [customer].[CompanyName]
      FROM [Customers] AS [customer]
     WHERE [customer].[CompanyName] LIKE 'A%'
     UNION ALL
    SELECT [customer].[CompanyName]
      FROM [Customers] AS [customer]
     WHERE [customer].[CompanyName] LIKE 'E%'
    ```

    **Gremlin**

    ```groovy
    gremlin> g.V().hasLabel("customer").union(
               filter {it.get().value("company")[0] == "A"},
               filter {it.get().value("company")[0] == "E"}).values("company")
    ==>Alfreds Futterkiste
    ==>Ana Trujillo Emparedados y helados
    ==>Antonio Moreno Taquería
    ==>Around the Horn
    ==>Eastern Connection
    ==>Ernst Handel
    ```

18. 创建、更新、删除

    **SQL**

    ```sql
    INSERT INTO [Categories] ([CategoryName], [Description])
         VALUES (N'Merchandising', N'Cool products to promote Gremlin')
    
    INSERT INTO [Products] ([ProductName], [CategoryID])
         SELECT TOP (1) N'Red Gremlin Jacket', [CategoryID]
           FROM [Categories]
          WHERE [CategoryName] = N'Merchandising'
    
    UPDATE [Products]
       SET [Products].[ProductName] = N'Green Gremlin Jacket'
     WHERE [Products].[ProductName] = N'Red Gremlin Jacket'
    
    DELETE FROM [Products]
     WHERE [Products].[ProductName] = N'Green Gremlin Jacket'
    
    DELETE FROM [Categories]
     WHERE [Categories].[CategoryName] = N'Merchandising'
    ```

    **Gremlin**

    ```groovy
    gremlin> c = graph.addVertex(label, "category",
                       "name", "Merchandising",
                       "description", "Cool products to promote Gremlin")
    ==>v[0]
    gremlin>
    gremlin> p = graph.addVertex(label, "product",
                       "name", "Red Gremlin Jacket")
    ==>v[3]
    gremlin>
    gremlin> p.addEdge("inCategory", c)
    ==>e[5][3-inCategory->0]
    gremlin>
    gremlin> g.V().has("product", "name", "Red Gremlin Jacket").
                   property("name", "Green Gremlin Jacket").iterate()
    gremlin>
    gremlin> p.remove()
    ==>null
    gremlin> g.V().has("category", "name", "Merchandising").drop()
    ```

​	

