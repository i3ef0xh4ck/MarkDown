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

## 根据关键字查询最后匹配的行
```shell
cat -n /opt/ubh/ubh-wxa/ubh-wxa.log |grep "卖出的商品" |tail -1
```

## 关键字检索分屏显示
```shell
cat -n /opt/ubh/ubh-wxa/ubh-wxa.log |grep "卖出的商品" |more
```
## 参考
[Linux 查找日志常用命令和技巧](http://okeeper.leanote.com/post/linux-%E6%9F%A5%E6%89%BE%E6%97%A5%E5%BF%97%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E5%92%8C%E6%8A%80%E5%B7%A7)
