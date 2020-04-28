Optional类是一个可以为null的容器对象

### 创建Optional对象

- empty()返回一个null
- of()有空就报错，确定是非空值使用
- ofNullable()传入任何都不会报错，不确定时使用

从Optional取出值 optional.get();取回值

### 对空指针处理

传统

```java
if(x != null){
	soutxxx
}
```

```java
Optional op = Optional.ofNullable(x);
boolean x = op.isPresent()
```

opt.orElse("xxxx")判断是否为空，是空输出参数的内容

opt.orElseGet(()->"xxx")

Optionan.offNullable(str).map(s->"true").orElse("false")