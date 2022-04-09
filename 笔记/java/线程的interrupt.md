```java
package common;

public class InterruptTest1 {

    public static void main(String[] args) {
        Thread thread = new Thread() {
            @Override
            public void run() {
                while (true) {

                }
            }
        };
        thread.start();
        Thread main = Thread.currentThread();
        new Thread(()->{
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            main.interrupt();//使用thread.interrupt()无法达到打断join的目的
        }).start();
        try {
            thread.join();//理解join()调用后，阻塞的不是thread线程，阻塞的时main线程
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

