## 检索关键字
```shell
# 检索所有关键字所在行的行数
cat -n /opt/ubh/ubh-wxa/ubh-wxa.log |grep "卖出的商品"
```

## 打印前几行、后几行及中间一段
```shell
# 打印前50行
head -50 /opt/ubh/ubh-wxa/ubh-wxa.log
# 打印后50行
tail -50 /opt/ubh/ubh-wxa/ubh-wxa.log
# 打印第n行起的100行
sed -n 'n,n+99p' /opt/ubh/ubh-wxa/ubh-wxa.log
```