```java
public class TimerExample {

    MetricRegistry registry = new MetricRegistry();
    Timer timer = registry.timer("request-timer");



    public void execute(){
        ConsoleReporter reporter = ConsoleReporter.forRegistry(registry)
                .convertDurationsTo(TimeUnit.SECONDS)//单个请求的持续时间
                .build();

        reporter.start(10,TimeUnit.SECONDS);
        for(;;){
            handleRequest();
        }
    }

    public void handleRequest(){
        Timer.Context context = timer.time();
        try {
            TimeUnit.SECONDS.sleep(ThreadLocalRandom.current().nextInt(10));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }finally {
            context.stop();
        }
    }



    public static void main(String[] args) {

        TimerExample example = new TimerExample();
        example.execute();


    }
}
```

```log
23-3-26 23:30:07 ===============================================================

-- Timers ----------------------------------------------------------------------
request-timer
             count = 3
         mean rate = 0.29 calls/second
     1-minute rate = 0.38 calls/second
     5-minute rate = 0.40 calls/second
    15-minute rate = 0.40 calls/second
               min = 0.00 seconds
               max = 4.00 seconds
              mean = 2.06 seconds
            stddev = 1.64 seconds
            median = 2.00 seconds
              75% <= 4.00 seconds
              95% <= 4.00 seconds
              98% <= 4.00 seconds
              99% <= 4.00 seconds
            99.9% <= 4.00 seconds


23-3-26 23:30:17 ===============================================================

-- Timers ----------------------------------------------------------------------
request-timer
             count = 5
         mean rate = 0.25 calls/second
     1-minute rate = 0.36 calls/second
     5-minute rate = 0.39 calls/second
    15-minute rate = 0.40 calls/second
               min = 0.00 seconds
               max = 8.00 seconds
              mean = 4.04 seconds
            stddev = 2.69 seconds
            median = 4.00 seconds
              75% <= 5.00 seconds
              95% <= 8.00 seconds
              98% <= 8.00 seconds
              99% <= 8.00 seconds
            99.9% <= 8.00 seconds
```

