## Bigdecimal
BigDecimal 表示不可变的有符号的任意精度的十进制数（浮点数）。  
BigDecimal 由任意精度的整数非标度值 和 32 位的整数标度 (scale) 组成。如果为零或正数，则标度是小数点后的位数。如果为负数，则将该数的非标度值乘以 10 的负 scale 次幂。因此，BigDecimal 表示的数值是 (<b>unscaledValue × 10<sup>-scale</sup></b>)。
> 
> <b>IEEE 浮点数</b> 计算机中是用有限的连续字节保存浮点数的。在 IEEE 标准中，浮点数是将特定长度的连续字节的所有二进制位分割为特定宽度的符号域，指数域和尾数域三个域，其中保存的值分别用于表示给定二进制浮点数中的符号，指数和尾数。这样，通过尾数和可以调节的指数（所以称为"浮点"）就可以表达给定的数值了。

### 基本用法
* 商业计算使用 BigDecimal。
* 尽量使用参数类型为 String 的构造函数。
* BigDecimal 都是不可变的（immutable）的，在进行每一步运算时，都会产生一个新的对象，所以在做加减乘除运算时千万要保存操作后的值。

```java
public class Test {
	public static void main(String[] args) {
		BigDecimal bg1 = new BigDecimal("1.22");
		BigDecimal bg2 = new BigDecimal(1.23);
		
		bg1.multiply(bg2);
		System.out.println(bg1);// 运算结果不会保存在原对象中
		
		BigDecimal bg3 = bg1.multiply(bg2);
		System.out.println(bg3);// 运算结果会以新对象的形式返回
	}
}
```

### 关键原理
* BigDecimal 采用的是IEEE 浮点数表示法，该表示法的算法为 (<b>unscaledValue × 10<sup>-scale</sup></b>)。BigDecimal 的加减乘除是基于BigInteger与BigInteger之间的加减乘除实现的。

```java
public class BigDecimal extends Number implements Comparable<BigDecimal> {
    private final BigInteger intVal;
    private final int scale;
    private transient int precision;
	private final transient long intCompact;
	public BigDecimal multiply(BigDecimal multiplicand, MathContext mc) {
		//...
        return multiplyAndRound(this.intVal, multiplicand.intVal, productScale, mc);
		//...
    }
	private static BigDecimal multiplyAndRound(long x, long y, int scale, MathContext mc) {
		//...
		return doRound(res,mc);
	}
}
```

* 当 double 必须用作 BigDecimal 的源时，请注意，此构造方法提供了一个准确转换；它不提供与以下操作相同的结果：先使用 Double.toString(double)方法，然后使用 BigDecimal(String)构造方法，将 double 转换为 String。要获取该结果，请使用 static valueOf(double)方法。

```java
public static BigDecimal valueOf(double val) {
    return new BigDecimal(Double.toString(val));
}
```

* String 构造方法是完全可预知的：写入 newBigDecimal("0.1") 将创建一个 BigDecimal，它正好等于预期的 0.1。因此，比较而言，通常建议优先使用 String 构造方法。

```java
public BigDecimal(String val) {
    this(val.toCharArray(), 0, val.length());
}
```

### 实践应用
* 商业计算使用 BigDecimal。
* 尽量使用参数类型为 String 的构造函数。
* BigDecimal 都是不可变的（immutable）的，在进行每一步运算时，都会产生一个新的对象，所以在做加减乘除运算时千万要保存操作后的值。

```java
public class BigDecimal extends Number implements Comparable<BigDecimal> {
    private final BigInteger intVal;
    private final int scale;
    private transient int precision;
	private final transient long intCompact;
	public BigDecimal multiply(BigDecimal multiplicand, MathContext mc) {
		//...
        return multiplyAndRound(this.intVal, multiplicand.intVal, productScale, mc);
		//...
    }
	private static BigDecimal multiplyAndRound(long x, long y, int scale, MathContext mc) {
		//...
		res = new BigDecimal(BigInteger.valueOf(x).multiply(y*rsign), INFLATED, scale, 0);
		return doRound(res,mc);
	}
}
```

* BigDecimal 做乘除计算时，一定要设置精度和保留小数点位数(除法运算除不尽时，未设置精度和保留小数点位数会抛异常:java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result.)。

```java
public class Test {
	public static void main(String[] args) {
		BigDecimal d1 = new BigDecimal("0.3");  
		BigDecimal d2 = new BigDecimal("0.7");  
		BigDecimal d3 = d1.divide(d2,2,BigDecimal.ROUND_HALF_UP);// 四舍五入保留两位小数
		System.out.println(d3); 
		BigDecimal d4 = d1.divide(d2);// java.lang.ArithmeticException: Non-terminating decimal expansion; no exact representable decimal result.
		System.out.println(d4);  
	}
}
```

* BigDecimal计算时，单独放到try catch内。

### 参考文献  
[Java BigDecimal 详解](http://blog.csdn.net/jackiehff/article/details/8582449)  
[Java之道系列：BigDecimal如何解决浮点数精度问题](http://www.lxway.com/605846602.htm)  
[BigDecimal 的那些坑事儿](http://blog.csdn.net/ugg/article/details/8213666)  
[浮点数与IEEE 754](http://www.cnblogs.com/kingwolfofsky/archive/2011/07/21/2112299.html)  


