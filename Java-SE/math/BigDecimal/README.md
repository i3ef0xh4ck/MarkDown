# java.math.BigDecimal使用小结

## divide方法

### 使用BigDecimal.divide方法时一定要考虑：
1. 除数是否为0 
2. 商是否是无限小数

### 正确的使用方式
1. 判断除数是否为0，是0做另外的处理逻辑
2. 调用除法时同时指定商保留的小数位数

### 示例代码
```
@Test
public void divideCorrectUsage() {
    BigDecimal divisor = BigDecimal.ZERO, dividend = BigDecimal.ONE;
    if (divisor != BigDecimal.ZERO) {
        BigDecimal di = BigDecimal.ONE.divide(divisor);
        System.out.println(di);
    } else {
        System.out.println("divisor is zero.");
    }
}

```