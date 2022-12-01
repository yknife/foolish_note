### Gradle配置

```groovy
dependencies {
    implementation 'org.projectlombok:lombok:1.18.16'
    implementation 'ch.qos.logback:logback-classic:1.2.3'
}
```

### 配合lombok，达到最简单配置，只需要在类上增加@Slf4j注解

```java
@Slf4j
public class LogTest {

    public static void main(String[] args) {
        log.info("你好啊");
    }
}
```

