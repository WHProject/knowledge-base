知识点的整理应尽可能的在基本用法、关键原理、实践应用三个方面进行详细的记录说明，另外在整理过程中参考的文献应尽可能的贴出链接，便于以后翻看该知识点相关的详细内容。  

# 整理模版
## 知识点
简单介绍知识点。
### 基本用法
概要描述知识点的定义、基本用法。
### 关键原理
详细描述知识点关键部分的实现原理。
### 实践应用
详细介绍知识点的具体应用案例、实际使用时的注意要点。
### 参考文献  
[参考文献名称](引用参考文献链接) 

# 整理案例
## Bigdecimal
BigDecimal 由任意精度的整数非标度值 和 32 位的整数标度 (scale) 组成。如果为零或正数，则标度是小数点后的位数。如果为负数，则将该数的非标度值乘以 10 的负 scale 次幂。因此，BigDecimal 表示的数值是 (unscaledValue × 10-scale)。
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

### 参考文献  
[Java BigDecimal 详解](http://blog.csdn.net/jackiehff/article/details/8582449)  
