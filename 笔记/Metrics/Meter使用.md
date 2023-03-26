```java
public class MeterExample {

    MetricRegistry registry = new MetricRegistry();
    Meter tqs = registry.meter("tqs");

    public void execute(){
        ConsoleReporter reporter = ConsoleReporter.forRegistry(registry)
                .convertRatesTo(TimeUnit.MINUTES)//每分钟执行多少次
                .convertDurationsTo(TimeUnit.MILLISECONDS)
                .build();
        reporter.start(10,TimeUnit.SECONDS);
        for(;;){
            sleepSeconds();
            handleRequest(null);
        }

    }
    public void handleRequest(byte[] bytes){
        tqs.mark();
        sleepSeconds();
    }

    public void sleepSeconds(){
        try {
            TimeUnit.SECONDS.sleep(ThreadLocalRandom.current().nextInt(10));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {

        MeterExample meterExample = new MeterExample();
        meterExample.execute();

    }

}
```

```log
23-3-26 23:50:08 ===============================================================

-- Meters ----------------------------------------------------------------------
tqs
             count = 2
         mean rate = 11.47 events/minute
     1-minute rate = 12.00 events/minute
     5-minute rate = 12.00 events/minute
    15-minute rate = 12.00 events/minute


23-3-26 23:50:18 ===============================================================

-- Meters ----------------------------------------------------------------------
tqs
             count = 4
         mean rate = 11.72 events/minute
     1-minute rate = 11.04 events/minute
     5-minute rate = 11.80 events/minute
    15-minute rate = 11.93 events/minute
```

