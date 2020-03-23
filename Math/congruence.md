# Congruence and computer #
> <b>前提：m>1</b>
- 同余：整数a, b都能被m整除，则a, b同余
    - 整除写法：m|a, m|b ==> a，b能被m整除 
    - 同余写法：a≡b(mod m)
    - <b>Note：a, b 不是余数, 余数式子中没有表示出来，容易造成误解</b>
    - <b>a≡b (mod m), 则 m|(a-b)</b>

- <b>同余自反性</b>：a≡a(mod m) [注：a<m] 
    - 2 (mod 7) => 2%7 => 2
    - 9 (mod 7) => 9%7 => 2
    - 2≡9 (mod 7)&nbsp;&nbsp;or &nbsp;&nbsp;9≡2 (mod 7)

- <b>a, b变换 - 线性</b> 
    - 同余加法：a±b≡d±c (mod m);  
    - 同余乘法：a*b≡d*c (mod m)
    - 同余幂运算：a≡b (mod m), 则 a^n≡b^n (mod m)

- <b>模数m变换</b> 
    - 同余除法：ac≡bc (mod m), 则a≡b (mod m/gcd(c, m)). c!=0, gcd最大公约数
    - 整除换模：a≡b (mod m), m|n, 则 a≡b (mod n)

- <b>其他</b>
    - <b>0 (mod m) => 0</b>
    - <b>1 (mod m) => 1</b>

> <b>Compuer</b>
- 背景
    - int中正数存的是原码
    - int中负数存的是补码
    - int中则负数比正数多一个

- 计算机正负数计算
    - 从补码中得出负数值: <b>取反加1</b>
    - 从原码中得出补码：<b>减1取反</b>

- 计算机中减法，正负数表示，模运算的关系
    - 负数可以通过模运算转成正数存储起来
        1. -2 可以经过模16转换成 14

    - 在二进制中将最高位丢弃相当于减去对应的进制数，可以当成是一种取模操作
        1. 1110 -> 丢弃首位1 -> 0110 -> 相当减去相应的进制数(2^4) 即：对进行模16操作 
        2. 类似10进制对10取模