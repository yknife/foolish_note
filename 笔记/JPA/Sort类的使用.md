1. 定义排序表达式

   ```java
   Sort sort = Sort.by("firstname").ascending()
     .and(Sort.by("lastname").descending());
   ```

2. 使用更安全的API方式定义排序表达式

   ```java
   TypedSort<Person> person = Sort.sort(Person.class);
   
   Sort sort = person.by(Person::getFirstname).ascending()
     .and(person.by(Person::getLastname).descending());
   ```

   