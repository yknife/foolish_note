```java
public class WordCount {


    public static void main(String[] args) throws Exception {

        ExecutionEnvironment environment = ExecutionEnvironment.getExecutionEnvironment();
        DataSource<String> dataSource = environment.readTextFile("data/input.txt");

        FlatMapOperator<String, Tuple2<String, Integer>> wordAndOne = dataSource.flatMap(new FlatMapFunction<String, Tuple2<String,Integer>>() {
            @Override
            public void flatMap(String line, Collector<Tuple2<String, Integer>> collector) throws Exception {
                String[] words = line.split(" ");
                for (String word : words) {
                    Tuple2<String, Integer> tuple = Tuple2.of(word, 1);
                    collector.collect(tuple);
                }
            }
        });

        UnsortedGrouping<Tuple2<String, Integer>> groupBy = wordAndOne.groupBy(0);
        AggregateOperator<Tuple2<String, Integer>> sum = groupBy.sum(1);
        sum.print();
    }
}
```