1. 为什么不用偶数

   > *The value 31 was chosen because it is an odd prime. If it were even and the multiplication overflowed, information would be lost, as multiplication by 2 is equivalent to shifting. The advantage of using a prime is less clear, but it is traditional. A nice property of 31 is that the multiplication can be replaced by a shift and a subtraction for better performance: `31 \* i == (i << 5) - i``. Modern VMs do this sort of optimization automatically.*

   > *选择数字31是因为它是一个奇质数，如果选择一个偶数会在乘法运算中产生溢出，导致数值信息丢失，因为乘二相当于移位运算。选择质数的优势并不是特别的明显，但这是一个传统。同时，数字31有一个很好的特性，即乘法运算可以被移位和减法运算取代，来获取更好的性能：*`31 * i == (i << 5) - i`*，现代的 Java 虚拟机可以自动的完成这个优化。*

   > As Goodrich and Tamassia point out, If you take over 50,000 English words (formed  as the union of the word lists provided in two variants of Unix), using the constants  31, 33, 37, 39, and 41 will produce less than 7 collisions in each case. Knowing  this, it should come as no surprise that many Java implementations choose one of  these constants. 

   >1. 这个回答就很有实战意义了，告诉你用超过 5 千个单词计算 hashCode， 这个 hashCode 的运算使用 31、33、37、39 和 41 作为乘积，得到的碰撞结果，31 被使用就很正常了。
   >2. 以这种说法用不同的乘数测试碰撞概率。

2. 为什么不用比较小的数

   因为取值的范围小

3. 为什么使用31，相对于33，37

   jvm可以优化计算

   > 31 * i == (i << 5) - i

* hashcode源码公式

  > s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]

* 举例解释公式算法：

  ```java
  "abc".hashCode();
  计算hashCode要将字段转换成ASCII码值 a = 97, b = 98, c = 99 
  hash = 97 * 31 ^ (3 - 1) + 98 * 31 * (3 - 2) + 99 = 96354
  ```





### 3、[Hash](https://so.csdn.net/so/search?q=Hash&spm=1001.2101.3001.7020) 碰撞概率计算

接下来要做的事情就是根据 stackoverflow 的回答，统计出不同的乘积数对 10 万个单词的 hash 计算结果。

#### 3.1 读取单词字典表

```java
/**
 * 读取本地文件，单词表
 * @param url 单词表.txt文件
 * @return 单词集合(去重)
 */
public static Set<String> readWordList(String url) {
    Set<String> list = new HashSet<>();
    try {
        InputStreamReader isr = new InputStreamReader(new FileInputStream(url), StandardCharsets.UTF_8);
        BufferedReader br = new BufferedReader(isr);
        String line = "";
        while ((line = br.readLine()) != null) {
            String[] ss = line.split("\t");
            list.add(ss[1]);
        }
        br.close();
        isr.close();
    } catch (Exception ignore) {
        return null;
    }
    return list;
}
```

#### 3.2 Hash 计算[函数](https://so.csdn.net/so/search?q=函数&spm=1001.2101.3001.7020)

```java
/**
 * 根据不同乘数计算hashCode
 * @param str 字符串
 * @param multiplier 乘数
 * @return hash值
 */
public static Integer hashCode(String str, Integer multiplier) {
    int hash = 0;
    for (int i = 0; i < str.length(); i++) {
        hash = multiplier * hash + str.charAt(i);
    }
    return hash;
}
```

这个过程与原 HashCode 函数相比，只是使用可变参数，用于统计不同的乘数计算的 hash 值。

#### 3.3 Hash 碰撞概率计算

想计算碰撞很简单，也就是计算那些出现相同哈希值的数量，计算出碰撞总量即 可。这里的实现方式有很多，可以使用 set、map 也可以使用 java8 的 stream 流 统计 distinct。

```java
/**
 * 计算Hash碰撞概率
 */
private static RateInfo hashCollisionRate(Integer multiplier, List<Integer> hashCodeList) {
    int maxHash = hashCodeList.stream().max(Integer::compareTo).get();
    int minHash = hashCodeList.stream().min(Integer::compareTo).get();

    // 碰撞数
    int collisionCount = (int) (hashCodeList.size() - hashCodeList.stream().distinct().count());
    // 碰撞率
    double collisionRate = (collisionCount * 1.0) / hashCodeList.size();

    return new RateInfo(maxHash, minHash, multiplier, collisionCount, collisionRate);
}

public static List<RateInfo> collisionRateList(Set<String> strList, Integer... multipliers) {
    List<RateInfo> rateInfoList = new ArrayList<>();
    for (Integer multiplier : multipliers) {
        List<Integer> hashCodeList = new ArrayList<>();
        for (String str : strList) {
            Integer hashCode = hashCode(str, multiplier);
            hashCodeList.add(hashCode);
        }
        rateInfoList.add(hashCollisionRate(multiplier, hashCodeList));
    }
    return rateInfoList;
}
```

这里记录了 hash 最大值、hash 最小值、碰撞数、碰撞率

#### 3.4 [单元测试](https://so.csdn.net/so/search?q=单元测试&spm=1001.2101.3001.7020)

```java
private Set<String> words;

    @Before
    public void before() {
        System.out.println("abc".hashCode());
        // 读取文件，103976个英语单词库.txt
        words = FileUtil.readWordList("E:\\workspace\\interview-master\\interview-master\\interview-03\\103976个英语单词库.txt");
    }

    @Test
    public void test_collisionRate() {
        System.out.println("单词数量：" + words.size());
        List<RateInfo> rateInfoList = HashCode.collisionRateList(words, 2, 3, 5, 7, 17, 31, 32, 33, 39, 41, 199);
        for (RateInfo rate : rateInfoList) {
            System.out.println(String.format("乘数 = %4d, 最小Hash = %11d, 最大Hash = %10d, 碰撞数量 =%6d, 碰撞概率 = %.4f%%", rate.getMultiplier(), rate.getMinHash(), rate.getMaxHash(), rate.getCollisionCount(), rate.getCollisionRate() * 100));
        }
    }

```

1. 先读取英文单词表中的 10万 个单词，之后做 hash 计算。
2. 在 hash 计算中把单词表传递进去，同时还有乘积数；2, 3, 5, 7, 17, 31, 32, 33, 39, 41, 199，最终返回一个 list 结果并输出。
3. 这里主要验证同一批单词，对于不同乘积数会有怎么样的 hash 碰撞结 果。

**测试结果：**

```
单词数量：103976
乘数 =    2, 最小Hash =          97, 最大Hash = 1842581979, 碰撞数量 = 60382, 碰撞概率 = 58.0730%
乘数 =    3, 最小Hash = -2147308825, 最大Hash = 2146995420, 碰撞数量 = 24300, 碰撞概率 = 23.3708%
乘数 =    5, 最小Hash = -2147091606, 最大Hash = 2147227581, 碰撞数量 =  7994, 碰撞概率 = 7.6883%
乘数 =    7, 最小Hash = -2147431389, 最大Hash = 2147226363, 碰撞数量 =  3826, 碰撞概率 = 3.6797%
乘数 =   17, 最小Hash = -2147238638, 最大Hash = 2147101452, 碰撞数量 =   576, 碰撞概率 = 0.5540%
乘数 =   31, 最小Hash = -2147461248, 最大Hash = 2147444544, 碰撞数量 =     2, 碰撞概率 = 0.0019%
乘数 =   32, 最小Hash = -2007883634, 最大Hash = 2074238226, 碰撞数量 = 34947, 碰撞概率 = 33.6106%
乘数 =   33, 最小Hash = -2147469046, 最大Hash = 2147378587, 碰撞数量 =     1, 碰撞概率 = 0.0010%
乘数 =   39, 最小Hash = -2147463635, 最大Hash = 2147443239, 碰撞数量 =     0, 碰撞概率 = 0.0000%
乘数 =   41, 最小Hash = -2147423916, 最大Hash = 2147441721, 碰撞数量 =     1, 碰撞概率 = 0.0010%
乘数 =  199, 最小Hash = -2147459902, 最大Hash = 2147480320, 碰撞数量 =     0, 碰撞概率 = 0.0000%
```

![碰撞概率统计图](/Users/yknife/Documents/笔记/截图/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxNTYyMQ==,size_16,color_FFFFFF,t_70.png)

以上就是不同的乘数下的 hash 碰撞结果图标展示，从这里可以看出如下信息；

1. 乘数是 2 时，hash 的取值范围比较小，基本是堆积到一个范围内了，后 面内容会看到这块的展示。
2. 乘数是 3、5、7、17 等，都有较大的碰撞概率
3. 乘数是 31 的时候，碰撞的概率已经很小了，基本稳定。
4. 顺着往下看，你会发现 199 的碰撞概率更小，这就相当于一排奇数的茅 坑量多，自然会减少碰撞。但这个范围值已经远超过 int 的取值范围 了，如果用此数作为乘数，又返回 int 值，就会丢失数据信息。

### Hash 值[散列](https://so.csdn.net/so/search?q=散列&spm=1001.2101.3001.7020)分布

除了以上看到哈希值在不同乘数的一个碰撞概率后，关于散列表也就是 hash， 还有一个非常重要的点，那就是要尽可能的让数据散列分布。只有这样才能减少 hash 碰撞次数，也就是后面章节要讲到的 hashMap 源码。

那么怎么看散列分布呢？如果我们能把 10 万个 hash 值铺到图表上，形成的一张 图，就可以看出整个散列分布。但是这样的图会比较大，当我们缩小看后，就成 一个了大黑点。所以这里我们采取分段统计，把 2 ^ 32 方分 64 个格子进行存 放，每个格子都会有对应的数量的 hash 值，最终把这些数据展示在图表上。

#### 4.1 哈希值分段存放

```java
public static Map<Integer, Integer> hashArea(List<Integer> hashCodeList) {
    Map<Integer, Integer> statistics = new LinkedHashMap<>();
    int start = 0;
    for (long i = 0x80000000; i <= 0x7fffffff; i += 67108864) {
        long min = i;
        long max = min + 67108864;
        // 筛选出每个格子里的哈希值数量，java8流统计
        int num = (int) hashCodeList.parallelStream().filter(x -> x >= min && x < max).count();
        statistics.put(start++, num);
    }
    return statistics;
}

public static Map<Integer, Integer> hashArea(Set<String> strList, Integer multiplier) {
    List<Integer> hashCodeList = new ArrayList<>();
    for (String str : strList) {
        Integer hashCode = hashCode(str, multiplier);
        hashCodeList.add(hashCode);
    }
    return hashArea(hashCodeList);
}
```

1. 这个过程是为了统计 int 取值范围内，每个 Hash 值存放到不同格子的数量。

#### 4.2 单元测试

```java
@Test
public void test_hashArea() {
    System.out.println(HashCode.hashArea(words, 2).values());
    System.out.println(HashCode.hashArea(words, 7).values());
    System.out.println(HashCode.hashArea(words, 31).values());
    System.out.println(HashCode.hashArea(words, 32).values());
    System.out.println(HashCode.hashArea(words, 199).values());
}
```

**统计报表**

本文中的哈希值是用整形表示的，整形的数值区间是 `[-2147483648, 2147483647]`，区间大小为 `2^32`。所以这里可以将区间等分成64个子区间，每个自子区间大小为 `2^26`。详细的分区对照表如下：

| 分区编号 | 分区下限    | 分区上限    | 分区编号 | 分区下限   | 分区上限   |
| -------- | ----------- | ----------- | -------- | ---------- | ---------- |
| 0        | -2147483648 | -2080374784 | 32       | 0          | 67108864   |
| 1        | -2080374784 | -2013265920 | 33       | 67108864   | 134217728  |
| 2        | -2013265920 | -1946157056 | 34       | 134217728  | 201326592  |
| 3        | -1946157056 | -1879048192 | 35       | 201326592  | 268435456  |
| 4        | -1879048192 | -1811939328 | 36       | 268435456  | 335544320  |
| 5        | -1811939328 | -1744830464 | 37       | 335544320  | 402653184  |
| 6        | -1744830464 | -1677721600 | 38       | 402653184  | 469762048  |
| 7        | -1677721600 | -1610612736 | 39       | 469762048  | 536870912  |
| 8        | -1610612736 | -1543503872 | 40       | 536870912  | 603979776  |
| 9        | -1543503872 | -1476395008 | 41       | 603979776  | 671088640  |
| 10       | -1476395008 | -1409286144 | 42       | 671088640  | 738197504  |
| 11       | -1409286144 | -1342177280 | 43       | 738197504  | 805306368  |
| 12       | -1342177280 | -1275068416 | 44       | 805306368  | 872415232  |
| 13       | -1275068416 | -1207959552 | 45       | 872415232  | 939524096  |
| 14       | -1207959552 | -1140850688 | 46       | 939524096  | 1006632960 |
| 15       | -1140850688 | -1073741824 | 47       | 1006632960 | 1073741824 |
| 16       | -1073741824 | -1006632960 | 48       | 1073741824 | 1140850688 |
| 17       | -1006632960 | -939524096  | 49       | 1140850688 | 1207959552 |
| 18       | -939524096  | -872415232  | 50       | 1207959552 | 1275068416 |
| 19       | -872415232  | -805306368  | 51       | 1275068416 | 1342177280 |
| 20       | -805306368  | -738197504  | 52       | 1342177280 | 1409286144 |
| 21       | -738197504  | -671088640  | 53       | 1409286144 | 1476395008 |
| 22       | -671088640  | -603979776  | 54       | 1476395008 | 1543503872 |
| 23       | -603979776  | -536870912  | 55       | 1543503872 | 1610612736 |
| 24       | -536870912  | -469762048  | 56       | 1610612736 | 1677721600 |
| 25       | -469762048  | -402653184  | 57       | 1677721600 | 1744830464 |
| 26       | -402653184  | -335544320  | 58       | 1744830464 | 1811939328 |
| 27       | -335544320  | -268435456  | 59       | 1811939328 | 1879048192 |
| 28       | -268435456  | -201326592  | 60       | 1879048192 | 1946157056 |
| 29       | -201326592  | -134217728  | 61       | 1946157056 | 2013265920 |
| 30       | -134217728  | -67108864   | 62       | 2013265920 | 2080374784 |
| 31       | -67108864   | 0           | 63       | 2080374784 | 2147483648 |

##### 4.2.1 乘数 2 的散列

![乘数为 2 时 散列结果](/Users/yknife/Documents/笔记/截图/220228a.jpg)

- 乘数是 2 的时候，散列的结果基本都堆积在中间，没有很好的散列。

##### 4.2.2 乘数 7 的散列

![乘数为 7 时 散列结果](/Users/yknife/Documents/笔记/截图/220228b.jpg)

- 乘数是 7 的时候，散列的结果虽然有分散开，但是不是很好的散列

##### 4.2.2 乘数 31 的散列

![乘数为 31 时 散列结果](/Users/yknife/Documents/笔记/截图/220228c.jpg)

- 乘数是 31 的时候，散列的效果就非常明显了，基本在每个范围都有数据 存放。

##### 4.2.2 乘数 199 的散列

![乘数为 199 时 散列结果](/Users/yknife/Documents/笔记/截图/220228d.jpg)

- 乘数是 199 是不能用的散列结果，但是它的数据是更加分散的，从图上 能看到有两个小山包。但因为数据区间问题会有数据丢失问题，所以不 能选择。

### 5、总结

- 以上主要介绍了 hashCode 选择 31 作为乘数的主要原因和实验数据验证，算是一个散列的数据结构的案例讲解
- 选择使用 31 作为乘数是基于大量数据验证的，可以保证数据的 hash 值尽可能散列，降低碰撞概率，尽可能减少 Hash 冲突。



### 参考

1. https://stackoverflow.com/questions/299304/why-does-javas-hashcode-in-string-use-31-as-a-multiplier
2. https://blog.csdn.net/qq5621/article/details/113867605
3. https://www.cnblogs.com/nxzblogs/p/10666209.html



