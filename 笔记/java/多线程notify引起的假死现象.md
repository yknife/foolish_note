```java
package common;

import java.util.stream.Stream;

public class ProducerConsumerTest {

    private volatile int i = 0;

    private final Object lock = new Object();

    private volatile boolean isProduced = false;

    public void produce(){
        synchronized (lock){
            if(isProduced){
                try {
                    System.out.println(Thread.currentThread().getName()+":wait");
                    lock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }else {
                System.out.println(Thread.currentThread().getName()+"->"+(i++));
                lock.notify();
                isProduced = true;
            }
        }
    }

    public void consume(){
        synchronized (lock){
            if(!isProduced){
                try {
                    System.out.println(Thread.currentThread().getName()+":wait");
                    lock.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }else {
                System.out.println(Thread.currentThread().getName()+"->"+i);
                lock.notify();
                isProduced = false;
            }
        }
    }

    public static void main(String[] args) {
        ProducerConsumerTest test = new ProducerConsumerTest();
        Stream.of("p1","p2").forEach(p->{
            new Thread(()->{
                while (true)
                    test.produce();
            },p).start();
        });

        Stream.of("c1","c2").forEach(c->{
            new Thread(()->{
                while (true)
                    test.consume();
            },c).start();
        });

    }
}
```

```java
p1->0
p1:wait //p1,p2,c1,c2
p2:wait //p2,c1,c2(p1)
c1->1 //c1,c2(p1,p2) c1唤醒一个线程进入抢锁队列，有可能是p1或者p2
c1:wait //[p1,p2],c1,c2([p1,p2])
p1->1 //p1,c2(p2,c1) 说明唤醒的是p1,p1唤醒一个线程进入抢锁队列，有可能是p2,c1
p1:wait // [p2,c1],p1,c2([p2,c1])
p2:wait //p2,c2(p1,c1) 说明唤醒是p2
c2->2 //c2(p1,p2,c1) //经典案例：唤醒的如果是c1，则必定出现假死
c2:wait //c2,[p1,p2,c1] ([_],[_])
c1:wait // c1,(c2,p1,p2)
        // (c1,c2,p1,p2) 经过推理，复现了最终4个线程都wait的情况，最终导致程序互相等待，并且没有人激活的局面
```

总结：因为notify()方法是随机唤醒wait中的一个线程，虽然理论上存在p和c相互唤醒并且程序能够正常执行的情况，但是有很大的概率在长时间的执行过程中产生4个线程都出现wait的情况。