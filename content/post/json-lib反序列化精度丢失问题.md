---
title: json-lib反序列化精度丢失问题
date: 2015-11-25 21:30:58
tags: 
 - java 
 - 学习 
 - 序列化 
categories: 
  - 学习笔记
---

最近在工作中,遇到一个问题,项目中某处使用了json-lib的2.4-jdk15版本.问题最终简化为
````
double amount = 6264583.33;
String jsonString = "{\"pi\":" + amount + "}";
JSONObject jsonObject = JSONObject.fromObject(jsonString);
System.out.println("转换前:" + jsonString);
System.out.println("转换后:" + jsonObject);
````
这个值输出的将会是6264583.5 这个值.这个问题.先google一下,很快赵到了
http://sourceforge.net/p/json-lib/bugs/116/ 于是,大概问题知道了.是json-lib的一个bug,但是这个bug怎么来的呢.结合这个bug下面的评论和debug代码,先以pi这个例子,很快走到了.

![json_create](/images/json_create.jpg)

可以看到json-lib走到了apache common-lang(2.5这个版本) 的`NumberUtils.createNumber`处,此时String的还是对的.
继续单步,来到这个方法里面

![json_mant_dec](/images/json_mant_dec.jpg)

可以看到这里小数部分,整数部分也都还是对的.继续向下走.我擦.画风不太对.居然采用了先尝试float,发现没问题.然后就继续尝试double,我擦.直接数据就丢失了呀..

![json_float](/images/json_float.jpg)

![json_float_fluent](/images/json_float_fluent.jpg)

好吧..那么现在问题就便成了更简单的一个问题.

使用NumberUtils.createNumber 的bug.在bug issue里,有人提到.这个bug,apache官方已知.好的.
https://issues.apache.org/jira/browse/LANG-693
然后在这里有官方的一次修复,修复记录在这里.3.2版本已经修复.
http://svn.apache.org/viewvc?view=revision&revision=1484263
可以看到是对小数部分的长度进行了判断.如果小于7位,就用float转换,如果大于7,小于16,就用double,如果还大,就用BigDecimal.

![json_common_lang3_fix](/images/json_common_lang3_fix.jpg)

````
n = org.apache.commons.lang3.math.NumberUtils.createNumber("3.14159265358");
System.out.println("lang3_createNumber_3.14159265358---->" + n + "->精度正常");
````
于是我继续debug,看common-lang3的修复情况,好像确实是修复了.但是对于我出现的问题1.6264583.33 这个数字,还是出现了精度丢失,因为这里小数部分小于7位,所以尝试使用float转换,直接丢失精度

![lang3_float_loss](/images/lang3_float_loss.jpg)

修复不完善..

于是提个bug :https://issues.apache.org/jira/browse/LANG-1187 等回复.

继续.公司内部一般使用fastjson,那么如果我使用fastjson,有问题吗? 发现没有问题.

````
Object o = com.alibaba.fastjson.JSONObject.parse("3.14159265358");
System.out.println("fastjson_createNumber_3.14159265358---->" + o + "->精度正常");

o = com.alibaba.fastjson.JSONObject.parse("6264583.33");
System.out.println("fastjson_createNumber_6264583.33---->" + o + "->精度正常");
````

![fastjson_decimal](/images/fastjson_decimal.jpg)

可以看到,这里做转换的时候传递了一个是否是bigdecimal的标识.而这个标识默认是开启的.而且即使不开启..

![fastjson_first_decimal](/images/fastjson_first_decimal.jpg)

最坏的情况也是个double.所以数据不会丢失.

再顺便说一下,double的6264583.33 为什么转换到float会精度丢失,先看一下浮点数在计算机中怎么表示的
![double_present](/images/double_present.jpg)
找到一张图,这是double的标识和浮点数的计算.

而浮点数则是32位,1位符号位,8位幂,23位尾数,看测试代码

````
//double标识测试
double d = 6264583.33d;
long l = Double.doubleToLongBits(d);
System.out.println(Long.toBinaryString(l));

//float想要表示这个数字
float f = 6264583.33f;
int value = Float.floatToIntBits(f);
System.out.println(Integer.toBinaryString(value));

//double表示这个值
d = 6264583.5d;
l = Double.doubleToLongBits(d);
System.out.println(Long.toBinaryString(l));
````

输出结果(做一下分割对齐)
````
1 00000101010    111111001011100000111010101000111101011100001010010
1    00101010    1111110010111000001111
1 00000101010    111111001011100000111100000000000000000000000000000
````

注意看,第一行是6264583.33的double表示.而同样想要用float表示这个数字,发现幂,符号位,都是对的.但是因为尾数只有23位,所以四舍五入,将完整double的后几位进位1,变成了这个二进制表示法,这时候已经不准确了,
而这个数字呢.看第三行,会发现实际上是6264583.5的精确值表示.尾数位0都是可以省略的,因为按照公式计算也没啥作用.

如有问题,欢迎评论讨论.

附录:
完整的测试代码

````
public class App {
    public static void main(String[] args) {
//http://sourceforge.net/p/json-lib/bugs/116/

//2.4版本有问题
double pi = 3.14159265358;
String jsonString = "{\"pi\":" + pi + "}";
JSONObject jsonObject = JSONObject.fromObject(jsonString);
System.out.println("转换前:" + jsonString);
System.out.println("转换后:" + jsonObject);

double amount = 6264583.33;
jsonString = "{\"pi\":" + amount + "}";
jsonObject = JSONObject.fromObject(jsonString);
System.out.println("转换前:" + jsonString);
System.out.println("转换后:" + jsonObject);

//测试2.4版本引入的lang,这里面
Number n = org.apache.commons.lang.math.NumberUtils.createNumber("3.14159265358");
System.out.println("lang2_createNumber_3.14159265358---->" + n + "->精度丢失");

n = org.apache.commons.lang.math.NumberUtils.createNumber("6264583.33");
System.out.println("lang2_createNumber_6264583.33---->" + n + "->精度丢失");

//测试3.4版本,里面是根据小数部分的长度,选择是否使用float还是double,当小数部分大于7的时候,就会使用double
n = org.apache.commons.lang3.math.NumberUtils.createNumber("3.14159265358");
System.out.println("lang3_createNumber_3.14159265358---->" + n + "->精度正常");

//这种情况就会有问题,虽然小数部分是33,两位,但是实际上是个浮点数.所以还会丢失精度
n = org.apache.commons.lang3.math.NumberUtils.createNumber("6264583.33");
System.out.println("lang3_createNumber_6264583.33---->" + n + "->精度丢失");

//测试fastjson

Object o = com.alibaba.fastjson.JSONObject.parse("3.14159265358");
System.out.println("fastjson_createNumber_3.14159265358---->" + o + "->精度正常");

o = com.alibaba.fastjson.JSONObject.parse("6264583.33");
System.out.println("fastjson_createNumber_6264583.33---->" + o + "->精度正常");

//double标识测试
double d = 6264583.33d;
long l = Double.doubleToLongBits(d);
System.out.println(Long.toBinaryString(l));

//float想要表示这个数字
float f = 6264583.33f;
int value = Float.floatToIntBits(f);
System.out.println(Integer.toBinaryString(value));

//double表示这个值
d = 6264583.5d;
l = Double.doubleToLongBits(d);
System.out.println(Long.toBinaryString(l));
     }
}
````