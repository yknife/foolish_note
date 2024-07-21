## 转换算子

### 映射（map）

### 过滤（filter）

### 扁平映射（flatMap）

### 类型擦除

* lambda与匿名内部类的区别。当算子使用lambda表达式来书写处理逻辑时，lambda方法中的参数或者返回值中如果存在泛型的，泛型的信息不会保存在字节码文件中（匿名内部类虽然也会被泛型擦除，但是字节码中Signature中有，可以通过反射机制获取）。

* 何时需要使用returns暗示类型。在lambda方法的参数或返回值中出现了泛型，就需要使用returns暗示。

  ```java
  public static void main(String[] args) throws Exception {
          StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
          env.setParallelism(1);
          DataStreamSource<WaterSensor> source = env.fromElements(
                  new WaterSensor("s1", 1L, 1),
                  new WaterSensor("s2", 1L, 1),
                  new WaterSensor("s3", 1L, 1)
          );
          source.map(s-> Tuple2.of(s.getId(),1))
          //需要使用，因为Tuple2是一个泛型类
                  .returns(Types.TUPLE(Types.STRING,Types.INT))
                  .print();
          env.execute();
      }
  //public class Tuple2<T0, T1> extends Tuple{}
  
  
  public static void main(String[] args) throws Exception {
          StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
          env.setParallelism(1);
          DataStreamSource<WaterSensor> source = env.fromElements(
                  new WaterSensor("s1", 1L, 1),
                  new WaterSensor("s2", 1L, 1),
                  new WaterSensor("s3", 1L, 1)
          );
    			//不需要使用，因为返回值是String类型，不包含泛型
          source.map(s-> s.getId())
                  .print();
          env.execute();
      }
  ```

  