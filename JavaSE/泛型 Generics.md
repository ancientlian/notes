# 为什么用？
1. **在编译时会有更强的类型检查**
2. **不需类型转化**
3. **使算法更加通用**

没有泛型
```java
List list = new ArrayList();
list.add("hello");
String s = (String) list.get(0);
```

使用泛型
```java
List<String> list = new ArrayList<String>();
list.add("hello");
String s = list.get(0);   // no cast
```

# 泛型使用

## 泛型类

```java
public class Info<T> {
	private T value;
    
    public T getValue() {
        return value;
    }
    
    public void setValue(T value) {
        this.value = value;
    }
}

```

## 泛型方法
```java
public class Util {
    public static <K, V> boolean compare(Pair<K, V> p1, Pair<K, V> p2) {
        return p1.getKey().equals(p2.getKey()) &&
               p1.getValue().equals(p2.getValue());
    }
}

public class Pair<K, V> {

    private K key;
    private V value;

    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }

    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    public K getKey()   { return key; }
    public V getValue() { return value; }
}
```

## 泛型接口
```java
public interface Content<T> {
    T text();
}
```


# 通配符 Wildcards
> In generic code, the question mark (?), called the _wildcard_, represents an unknown type. 

 
## 上边界 Upper Bounded Wildcards

使用`extends`关键字
示例：
```java
public static void process(List<? extends Foo> list) { /* ... */ }
```

> The upper bounded wildcard, `<? extends Foo>`, where Foo is any type, matches Foo and any subtype of Foo. The process method can access the list elements as type Foo

```java
public static void process(List<? extends Foo> list) {
    for (Foo elem : list) {
        // ...
    }
}
```

计算和示例：
```java
public static double sumOfList(List<? extends Number> list) {
    double s = 0.0;
    for (Number n : list)
        s += n.doubleValue();
    return s;
}
```

使用方法
```java
List<Integer> li = Arrays.asList(1, 2, 3);
System.out.println("sum = " + sumOfList(li));


List<Double> ld = Arrays.asList(1.2, 2.3, 3.5);
System.out.println("sum = " + sumOfList(ld));
```


## 无边界 Unbounded Wildcards

直接用`?`即可

### 应用场景

- 可以使用 Object 类中提供的功能来实现的方法。
- 使用不依赖于类型参数的泛型类中的方法，如`List.size` ， `List.clear`， 大多数`Class<T>` 里的方法都不依赖于`T`

### 示例
```java
public static void printList(List<?> list) {
    for (Object elem: list)
        System.out.print(elem + " ");
    System.out.println();
}

// 测试方法
List<Integer> li = Arrays.asList(1, 2, 3);
List<String>  ls = Arrays.asList("one", "two", "three");
printList(li);
printList(ls);
```


## 下边界 Lower Bounded Wildcards
使用`super`关键字

示例
我们希望接受`Integer`、`Number`、`Object`，因为Number和Object是Integer的父类
```java
public static void addNumbers(List<? super Integer> list) {
    for (int i = 1; i <= 10; i++) {
        list.add(i);
    }
}
```

# 类型擦除 Type Erasure

> Generics were introduced to the Java language to provide tighter type checks at compile time and to support generic programming. To implement generics, the Java compiler applies type erasure to:
> - Replace all type parameters in generic types with their bounds or Object if the type parameters are unbounded. The produced bytecode, therefore, contains only ordinary classes, interfaces, and methods.
> - Insert type casts if necessary to preserve type safety.
> - Generate bridge methods to preserve polymorphism in extended generic types.
> 
Type erasure ensures that no new classes are created for parameterized types; consequently, generics incur no runtime overhead.

- 把泛型中的所有类型参数替换为 Object，如果指定类型边界，则使用类型边界来替换。因此，生成的字节码仅包含普通的类，接口和方法。
- 擦除出现的类型声明，即去掉` <>` 的内容。比如 `T get() `方法声明就变成了 `Object get() `；`List<String>` 就变成了` List`。如有必要，插入类型转换以保持类型安全。
- 生成桥接方法以保留扩展泛型类型中的多态性。类型擦除确保不为参数化类型创建新类；因此，泛型不会产生运行时开销。

## 泛型类型擦除

代码
```java
public class Node<T> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
```

用`Object`代替无边界的泛型参数`T`
```java
public class Node {

    private Object data;
    private Node next;

    public Node(Object data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Object getData() { return data; }
    // ...
}
```

就算是有边界值也是同理
```java
public class Node<T extends Comparable<T>> {

    private T data;
    private Node<T> next;

    public Node(T data, Node<T> next) {
        this.data = data;
        this.next = next;
    }

    public T getData() { return data; }
    // ...
}
```

擦除结果
```java
public class Node {

    private Comparable data;
    private Node next;

    public Node(Comparable data, Node next) {
        this.data = data;
        this.next = next;
    }

    public Comparable getData() { return data; }
    // ...
}
```

## 方法擦除

前
```java
// Counts the number of occurrences of elem in anArray.
//
public static <T> int count(T[] anArray, T elem) {
    int cnt = 0;
    for (T e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
```
后
```java
public static int count(Object[] anArray, Object elem) {
    int cnt = 0;
    for (Object e : anArray)
        if (e.equals(elem))
            ++cnt;
        return cnt;
}
```


## 桥方法 Bridge Methods




# 泛型命名约定

- E - Element (used extensively by the Java Collections Framework)
- K - Key
- V - Value
- T - Type
- N - Number
- S,U,V etc. - 2nd, 3rd, 4th types

# 参考文章

[https://docs.oracle.com/javase/tutorial/java/generics/index.html](https://docs.oracle.com/javase/tutorial/java/generics/index.html)
[https://www.cnblogs.com/XiiX/p/14719568.html](https://www.cnblogs.com/XiiX/p/14719568.html)
[https://www.yuque.com/snailclimb/mf2z3k/ipqccd](https://www.yuque.com/snailclimb/mf2z3k/ipqccd#acb25cb7)
