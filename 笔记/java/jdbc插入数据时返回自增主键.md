```java
@Test
public void testJDBCInsertUser(){
    Connection connection = null;
    PreparedStatement ps = null;
    ResultSet generatedKeys = null;
    try {
        Class.forName("com.mysql.jdbc.Driver");
        connection = DriverManager.getConnection("jdbc:mysql://localhost:3306/mybatis", "root", "1");
        ps = connection.prepareStatement("insert into t_user values(null,'lilei',32,'2022-01-01')", Statement.RETURN_GENERATED_KEYS);//Statement.RETURN_GENERATED_KEYS 表示返回自生成主键
        int i = ps.executeUpdate();
        generatedKeys = ps.getGeneratedKeys();//获得生成的主键游标
        while (generatedKeys.next()){
            int anInt = generatedKeys.getInt(1);//返回int类型的主键
            System.out.println(anInt);
        }
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    } catch (SQLException throwables) {
        throwables.printStackTrace();
    } finally {
        if(generatedKeys!=null){
            try {
                generatedKeys.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(ps!=null){
            try {
                ps.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        if(connection!=null){
            try {
                connection.close();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
    }
}
```