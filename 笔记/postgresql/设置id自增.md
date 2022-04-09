1. 创建seq

   ```sql
   CREATE SEQUENCE user_id_seq START 10;
   ```

2. 设置id字段默认值

   ```sql
   nextval('user_id_seq')
   ```

3. 重置seq

   ```sql
   alter sequence sequence_name restart with 1000
   ```

4. 验证

   ```sql
   SELECT nextval('sequence_name');
   ```

5. java实体

   ```java
   @Id
   @GeneratedValue(strategy = GenerationType.SEQUENCE,generator="user_seq")
   @SequenceGenerator(name="user_seq", sequenceName="user_id_seq",allocationSize = 1)
   Integer id;
   ```