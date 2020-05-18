简称闭包

允许把函数作为一个方法的参数

不是所有接口都可以使用lambda表达式来实现。

lambda规定接口中**只能**有一个必须需要被实现的方法

default修饰的方法会有默认实现，不影响lambda使用

接口注解@unctionalIntrtface配合lambda使用



Lambda 语法：

```
-无参无返回值: 
         Runnable runnable = () -> System.out.println("helloword");
```

 

-有参无返回值:

```
         Consumer<String> consumer = (x) -> System.out.println("helloword");
```

 

-如果只有一个参数，参数的小括号可以省略:

```
         Consumer<String> consumer = x -> System.out.println("helloword");
```

 

-如果有两个参数，而且有返回值:

```
Comparator<Integer> comparator=(x,y) -> {
    return Integer.compare(x,y);
};
```

 

-如果有两个参数，实现只有一行代码，花括号和return语句可以省略:

```
Comparator<Integer> comparator = (Integer x, Integer y) -> Integer.compare(x, y);
```

 

-如果有两个参数，等号左边使用了参数化类型，参数部分可以不写类型（编译器-类型推断），-实现只有一行代码，花括号可以省略

```
Comparator<Integer> comparator1 = (x, y) -> Integer.compare(x, y);
```

 

 

函数式接口：

@FunctionInterface

自定义泛型-创建接口-加注解=函数式接口

 

四大函数式接口

```
Consumer：消费性处理某事
fun("helloword",(x)-> System.out.print(x.toUpperCase()));
```

**private void** fun(String s, Consumer<String> consumer) {
   consumer.accept(s);
 }

```
 
```

 

Supplier：供给性，提供一个return

```
private List<Double> getRandomNumbalList(int num, Supplier<Double> supplier) {

    List<Double> doubles = new ArrayList<>();
    for (int i = 0; i < num; i++) {
        Double randoms = supplier.get();
        doubles.add(randoms);
    }
    return doubles;
}
```

 

Predicate：断言,传入后进行比较，执行lambda的内容

```
public List<String> filterList(List<String> datas, Predicate<String> predicate) {
    List<String> resultList = new ArrayList<>();
    for (String aa : datas) {
        if (predicate.test(aa)) {
            resultList.add(aa);
        }
    }

    return resultList;
}
```

 

Function：函数型，两个参数（参数，返回值类型）

```
public String handler(String s, Function<String, String> function) {
    return function.apply(s);
}
```

 