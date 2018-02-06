1. `stream.flatMapValues`  

```
//topic strings存有的消息：key:key value:hello world
KStream<String, String> stream = builder.stream("strings", Consumed.with(Serdes.String(), Serdes.String()));
stream.print(Printed.toSysOut());
/**打印：
[KSTREAM-SOURCE-0000000000]: key, "hello world"
*/

// flatMapValues 可以将一条消息的value转换成另一个类型：
//1.将消息内容装换成List<String>
KStream<String, String>  stream1 = stream.flatMapValues(value -> Arrays.asList(value.split(" ")));
stream1.print(Printed.toSysOut());
/**打印：
[KSTREAM-FLATMAPVALUES-0000000002]: key, "hello
[KSTREAM-FLATMAPVALUES-0000000002]: key, world"
*/

//2.将消息内容转换成另一个对象
KStream<String, Diji> stream1 = stream.flatMapValues(value -> {
            List<String> names = Arrays.asList(value.split(" "));
            List<Diji> dijis = Lists.newArrayList();
            names.forEach(e -> dijis.add(new Diji(e)));
            return dijis;
        });
/**打印：
[KSTREAM-FLATMAPVALUES-0000000002]: key, Diji{dijiId=0, provinceId=0, dijiName='"hello'}
[KSTREAM-FLATMAPVALUES-0000000002]: key, Diji{dijiId=0, provinceId=0, dijiName='world"'}
*/
```
总结：
KStream.flatMapValues()可以将一条消息记录拆分成多条消息记录，且消息记录类型可以改变，但是键还是使用原来的键。
