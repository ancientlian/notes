[Java Stream.docx](https://www.yuque.com/attachments/yuque/0/2022/docx/25408520/1671081730107-585a0116-ffe2-4ef8-aac6-b6d8f862416d.docx?_lake_card=%7B%22src%22%3A%22https%3A%2F%2Fwww.yuque.com%2Fattachments%2Fyuque%2F0%2F2022%2Fdocx%2F25408520%2F1671081730107-585a0116-ffe2-4ef8-aac6-b6d8f862416d.docx%22%2C%22name%22%3A%22Java%20Stream.docx%22%2C%22size%22%3A21668%2C%22type%22%3A%22application%2Fvnd.openxmlformats-officedocument.wordprocessingml.document%22%2C%22ext%22%3A%22docx%22%2C%22source%22%3A%22%22%2C%22status%22%3A%22done%22%2C%22mode%22%3A%22title%22%2C%22download%22%3Atrue%2C%22taskId%22%3A%22uded3a86a-de55-453e-a0bc-c682d53c5b1%22%2C%22taskType%22%3A%22upload%22%2C%22__spacing%22%3A%22both%22%2C%22id%22%3A%22uff7a34bc%22%2C%22margin%22%3A%7B%22top%22%3Atrue%2C%22bottom%22%3Atrue%7D%2C%22card%22%3A%22file%22%7D)

| 中间操作
intermediate operations | 无状态 | 过滤      filter |
| --- | --- | --- |
|  |  | 映射      map |
|  |  | 扁平化    flatMap |
|  |  | 遍历      peek |
|  | 有状态 | 去重     distinct |
|  |  | 跳过     skip |
|  |  | 截断     limit |
|  |  | 排序     sorted |
| 终端操作
terminal operations | 非短路 | 遍历       forEach、forEachOrdered |
|  |  | 规约       reduce |
|  |  | 最大值     max |
|  |  | 聚合       collect |
|  |  | 最小值     min |
|  |  | 计数       count |
|  | 短路 | 所有匹配    allMatch |
|  |  | 任意匹配    anyMatch |
|  |  | 不匹配      noneMatch |
|  |  | 查找首个    findFirst |
|  |  | 查找任意    findAny |


# Collector

- collect()（操作实现Collector接口的集合）
- Collector（接口）
- Collectors（工具类）

收集器（Collector）的作用为：将流中元素累积成一个结果作用于终端操作collect()上

## 集合收集
```java
/**
 * 集合收集
 */
@Test
public void toList() {
    List<Sku> list = CartService.getCartSkuList();
    List<Sku> result = list.stream()
            .filter(sku -> sku.getTotalPrice() > 100)
            .collect(Collectors.toList());
    System.out.println(JSON.toJSONString(result));
}
```
## 集合分组
```java
/**
 * 集合分组
 */
@Test
public void group() {
    List<Sku> list = CartService.getCartSkuList();
    // key=分组条件  value=元素集合  即Map<分组条件，结果集合>
    Map<Enum, List<Sku>> result = list.stream()
            .collect(Collectors.groupingBy(sku -> sku.getSkuCategory()));
    System.out.println(JSON.toJSONString(result));
}
```
## 集合分区
集合分区是分组的一种特例：分区是由一个谓词作为分区函数，分区函数返回一个boolean值最终将分区结果分为两组，一组为boolean=true的   一组为boolean=false
```java
/**
 * 集合分区
 */
@Test
public void partition() {
    List<Sku> list = CartService.getCartSkuList();
    Map<Boolean, List<Sku>> partition = list.stream()
            .collect(Collectors.partitioningBy(sku -> sku.getTotalPrice() > 100));
    System.out.println(JSON.toJSONString(partition));
}
```

# reduce操作
| **S.N.** | **方法说明** |
| --- | --- |
| 1 | `**Optional<T> reduce(BinaryOperator<T> accumulator);**`
对Stream中的数据通过累加器accumulator迭代计算，最终得到一个Optional对象 |
| 2 | `**T reduce(T identity, BinaryOperator<T> accumulator);**`
给定一个初始值identity，通过累加器accumulator迭代计算，得到一个同Stream中数据同类型的结果 |
| 3 | `**<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);**`
给定一个初始值identity，通过累加器accumulator迭代计算，得到一个identity类型的结果，第三个参数用于使用并行流时合并结果 |


```java
@FunctionalInterface
public interface BinaryOperator<T> extends BiFunction<T,T,T> {
}
 
@FunctionalInterface
public interface BiFunction<T, U, R> {
    R apply(T t, U u);
}
```

## `Optional<T> reduce(BinaryOperator<T> accumulator);`
reduce(BinaryOperator<T> accumulator)方法需要一个函数式接口参数，该函数式接口需要两个参数，返回一个结果(reduce中返回的结果会作为下次累加器计算的第一个参数)，也就是所讲的累加器。
```java
@Test
public void reduceTest2() {
    Optional accResult = Stream.of(6, 7, 8, 9,10)
            .reduce((acc, item) -> {
                System.out.println("acc : " + acc);
                acc += item;
                System.out.println("item: " + item);
                System.out.println("acc+ : " + acc);
                System.out.println("--------");
                return acc;
            });
    System.out.println(accResult);
}
```
结果：
```java
acc : 6
item: 7
acc+ : 13
--------
acc : 13
item: 8
acc+ : 21
--------
acc : 21
item: 9
acc+ : 30
--------
acc : 30
item: 10
acc+ : 40
--------
Optional[40]

Process finished with exit code 0

```

## `T reduce(T identity, BinaryOperator<T> accumulator);`
提供一个跟Stream中数据同类型的初始值identity，通过累加器accumulator迭代计算Stream中的数据，得到一个跟Stream中数据相同类型的最终结果
```java
@Test
public void reduceTest3() {
    int accResult = Stream.of(6, 7, 8, 9, 10)
            .reduce(200, (acc, item) -> {
                System.out.println("acc : " + acc);
                acc += item;
                System.out.println("item: " + item);
                System.out.println("acc+ : " + acc);
                System.out.println("--------");
                return acc;
            });
    System.out.println(accResult);
}
```
结果
```java
acc : 200
item: 6
acc+ : 206
--------
acc : 206
item: 7
acc+ : 213
--------
acc : 213
item: 8
acc+ : 221
--------
acc : 221
item: 9
acc+ : 230
--------
acc : 230
item: 10
acc+ : 240
--------
240

Process finished with exit code 0

```


## `<U> U reduce(U identity, BiFunction<U, ? super T, U> accumulator, BinaryOperator<U> combiner);`
首先看一下BiFunction的三个泛型类型分别是`U`、 `? super T`、`U`，参考BiFunction函数式接口apply方法定义可以知道，累加器累加器通过类型为`U`和`? super T`的两个输入值计算得到一个U类型的结果返回。也就是说这种reduce方法，提供一个不同于Stream中数据类型的初始值，通过累加器规则迭代计算Stream中的数据，最终得到一个同初始值同类型的结果。

```java
@Test
public void reduceTest4() {
    ArrayList<Integer> accResult_ = Stream.of(2, 3, 4)
            .reduce(Lists.newArrayList(1),
                    (acc, item) -> {
                        acc.add(item);
                        System.out.println("item: " + item);
                        System.out.println("acc+ : " + acc);
                        System.out.println("BiFunction");
                        return acc;
                    }, (acc, item) -> {
                        System.out.println("BinaryOperator");
                        acc.addAll(item);
                        System.out.println("item: " + item);
                        System.out.println("acc+ : " + acc);
                        System.out.println("--------");
                        return acc;
                    }
            );
    System.out.println("accResult_: " + accResult_);
}
```
结果
```java
item: 2
acc+ : [1, 2]
BiFunction
item: 3
acc+ : [1, 2, 3]
BiFunction
item: 4
acc+ : [1, 2, 3, 4]
BiFunction
accResult_: [1, 2, 3, 4]

Process finished with exit code 0

```

通过运行结果可以看出，第三个参数定义的规则并没有执行。这是因为reduce的第三个参数是在使用parallelStream的reduce操作时，合并各个流结果的，本例中使用的是stream，所以第三个参数是不起作用的。上述示例，提供一个只有一个元素1的arrayList，通过累加器迭代，将stream中的数据添加到arrayList中。


# 参考文章
[https://colobu.com/2016/03/02/Java-Stream/](https://colobu.com/2016/03/02/Java-Stream/)
[https://blog.csdn.net/weixin_41835612/article/details/83687078](https://blog.csdn.net/weixin_41835612/article/details/83687078)
