# 保留小数

## 使用BigDecimal的setScale方法

```java
double one = 5.864;
BigDecimal two = new BigDecimal(one);
double three = two.setScale(2,BigDecimal.ROUND_HALF_UP).doubleValue();
```

BigDecimal.ROUND_HALF_UP表示四舍五入
BigDecimal.ROUND_HALF_DOWN也是五舍六入
BigDecimal.ROUND_UP表示进位处理（就是直接加1）
BigDecimal.ROUND_DOWN表示直接去掉尾数

## 使用Sting自带的format方法

```java
double one = 5.864;
String  str = String.format("%.2f",one);
double four = Double.parseDouble(str);
```

## 使用NumberFormat设置最大小数位数

```java
double d = 5.864
NumberFormat nf = NumberFormat.getNumberInstance();
// 保留两位小数
nf.setMaximumFractionDigits(2); 
// 如果不需要四舍五入，可以使用RoundingMode.DOWN
nf.setRoundingMode(RoundingMode.UP);
System.out.println(nf.format(d));
```

RoundingMode.HALF_DOWN 表示 五舍六入，负数先取绝对值再五舍六入再负数，
RoundingMode.HALF_UP 表示 四舍五入，负数先取绝对值再五舍六入再负数。

## 使用DecimalFormat，#.00为保留两位小数
```java
import java.text.DecimalFormat;

double f = 5.864;
DecimalFormat df = new DecimalFormat("#.00");
System.out.println(df.format(f));
```
