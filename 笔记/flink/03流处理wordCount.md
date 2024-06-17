```java
public static void main(String[] args) throws Exception {
    StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();
    DataStreamSource<String> socketDS = env.socketTextStream("localhost", 8888);
    socketDS.flatMap((String line, Collector<Tuple2<String,Integer>> out)->{
        String[] words = line.split(" ");
        for(String word:words){
            Tuple2<String, Integer> tuple = Tuple2.of(word, 1);
            out.collect(tuple);
        }
    })
    .returns(Types.TUPLE(Types.STRING,Types.INT))
    .keyBy(t->t.f0)
    .sum(1)
            .print();

    env.execute();

}
```