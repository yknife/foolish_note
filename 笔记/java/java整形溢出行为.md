只有当x的起始值是偶数时才会发生这种情况.

> If an integer multiplication overflows, then the result is the low-order bits of the mathematical product as represented in some sufficiently large two’s-complement format. As a result, if overflow occurs, then the sign of the result may not be the same as the sign of the mathematical product of the two operand values.

> 如果整数乘法溢出，则结果是数学乘积的低位，以某种足够大的二进制补码格式表示。因此，如果发生溢出，则结果的符号可能与两个操作数值的数学乘积的符号不同。

如果我们以二进制格式而不是十进制格式打印数字,这会更加明显：

```java
public class IntegerOverflow {

    public static void main(String[] args) {

        int x = 10;

        int i = 0;

        for (i = 0; i <= 5; i++)

        {

            x *= x;

            System.out.println(Integer.toBinaryString(x));

        }

    }

}
```

输出：

1100100

10011100010000

101111101011110000100000000

1101111110000010000000000000000

0

0

> 如您所见,每次平方时,我们将零位数加倍.由于只保存了低位,因此每次将零加倍最终将导致零.请注意,如果x的起始值为奇数,则不会看到这些尾随零.相反,它会导致看似无关的数字,如溢出通常会产生.

```java
public class IntegerOverflow {undefined

public static void main(String[] args) {undefined

int x = 11;

int i = 0;

for (i = 0; i <= 5; i++)

{undefined

x *= x;

System.out.format("%-12d\t%s%n", x, Integer.toBinaryString(x));

}

}

}
```

输出：

121 1111001

14641 11100100110001

214358881 1100110001101101101101100001

772479681 101110000010110001101011000001

-1419655807 10101011011000011100010110000001

-1709061375 10011010001000011100101100000001

### 参考

1. https://blog.csdn.net/weixin_39534208/article/details/115597606