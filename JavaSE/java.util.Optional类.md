# 传统处理Null方式

## 深层质疑

每次不确定一个变量是否为null时，添加一个进一步嵌套的if块，增加了代码缩进的层数。
```java
public String getCarInsuranceName(Person person) {
    if (person != null) {
        Car car = person.getCar();
        if (car != null) {
            Insurance insurance = car.getInsurance();
            if (insurance != null) {
                return insurance.getName();
            }
        }
    }
    return "Unknown";

}
```

## 多路退出

每次遭遇null变量，都返回一个字符串常量“Unknown”。

```java
public String getCarInsuranceName2(Person person) {
    if (person == null) {
        return "Unknown";
    }
    Car car = person.getCar();
    if (car == null) {
        return "Unknown";
    }
    Insurance insurance = car.getInsurance();
    if (insurance == null) {
        return "Unknown";
    }
    return insurance.getName();
}
```

# Optional 类

变量存在时，Optional类只是对类简单封装。变量不存在时，缺失的值会被建模成一个“空”的Optional对象，由方法Optional.empty()返回。Optional.empty()方法是一个静态工厂方法，它返回Optional类的特定单一实例。

Optional类重构
```java
public class Person {
    private Optional<Car> car;
    public Optional<Car> getCar() { return car; }
}
public class Car {
    private Optional<Insurance> insurance;
    public Optional<Insurance> getInsurance() { return insurance; }
}
public class Insurance {
    private String name;
    public String getName() { return name; }
}
```

使用Optional丰富了语义，说明了可能为空的情况。但是保险insurance必须有string名字（这属于业务层面的判断）

# 应用
## 创建对象
### 声明一个空的Optional
```java
Optional<Car> optCar = Optional.empty();
```
### 依据一个非空值创建Optional
```java
Optional<Car> optCar = Optional.of(car);
```
如果car是一个null，这段代码会立即抛出一个NullPointerException，而不是等到你试图访问car的属性值时才返回一个错误。

### 可接受null的Optional
```java
Optional<Car> optCar = Optional.ofNullable(car);
```
如果car是null，那么得到的Optional对象就是个空对象。

## 使用 map 从 Optional 对象中提取和转换值
```java
Optional<Insurance> optInsurance = Optional.ofNullable(insurance);
Optional<String> name = optInsurance.map(Insurance::getName);
```

## 使用 flatMap 链接 Optional 对象
使用Optional获取car的保险公司名称
```java
public String getCarInsuranceName(Optional<Person> person) {
    return person.flatMap(Person::getCar)
                 .flatMap(Car::getInsurance)
                 .map(Insurance::getName)
                 .orElse("Unknown");
}
```
使用`orElse`的方法，当Optional的值为空时，它会为其设定一个默认值

## 使用 filter 剔除特定的值
如果Optional对象的值存在，并且它符合谓词的条件，filter方法就返回其值；否则它就返回一个空的Optional对象。
```java
Optional<Insurance> optInsurance = ...;
optInsurance.filter(insurance ->
            	"CambridgeInsurance".equals(insurance.getName()))
            .ifPresent(x -> System.out.println("ok"));
```


```java
public static String getCarInsuranceName(Optional<Person> person, int minAge) {
    return person.filter(p -> p.getAge() >= minAge)
                .flatMap(Person::getCar)
                .flatMap(Car::getInsurance)
                .map(Insurance::getName)
                .orElse("Unknown");
}
```

## 多种方法读取Optional实例中的变量值

`get()`：如果变量存在，它直接返回封装的变量值，否则就抛出一个`NoSuchElementException`异常
`orElse(T other)`：允许在Optional对象不包含值时提供一个默认值
`orElseGet(Supplier<? extends T> other)`：orElse方法的延迟调用版，Supplier方法只有在Optional对象不含值时才执行调用
`orElseThrow(Supplier<? extends X> exceptionSupplier)`：使用orElseThrow可以定制希望抛出的异常类型
`ifPresent(Consumer<? super T>)`：在变量值存在时执行一个作为参数传入的方法，否则就不进行任何操作

## 两个 Optional 对象的组合示例

找到满足该组合的最便宜的保险公司
```java
public Insurance findCheapestInsurance(Person person, Car car) {
    // 不同的保险公司提供的查询服务
    // 对比所有数据
    return cheapestCompany;
}
```

### 传统判空

```java
public Optional<Insurance> nullSafeFindCheapestInsurance (
        Optional<Person> person, Optional<Car> car){
    if (person.isPresent() && car.isPresent()) {
        return Optional.of(findCheapestInsurance(person.get(), car.get()));
    } else {
        return Optional.empty();
    }
}
```

### 一行实现
```java
public Optional<Insurance> nullSafeFindCheapestInsurance(
        Optional<Person> person, Optional<Car> car) {
    return person.flatMap(p -> car.map(c -> findCheapestInsurance(p, c)));
}
```

> 这段代码中，你对第一个Optional对象调用flatMap方法，如果它是个空值，传递给它的Lambda表达式不会执行，这次调用会直接返回一个空的Optional对象。反之，如果person对象存在，这次调用就会将其作为函数Function的输入，并按照与flatMap方法的约定返回一个`Optional<Insurance>`对象。
> 这个函数的函数体会对第二个Optional对象执行map操作，如果第二个对象不包含car，函数Function就返回一个空的Optional对象，整个`nullSafeFindCheapestInsuranc`方法的返回值也是一个空的Optional对象。
> 最后，如果person和car对象都存在，作为参数传递给map方法的Lambda表达式能够使用这两个值安全地调用原始的`findCheapestInsurance`方法，完成期望的操作。


# Optional 的实战示例
## 封装可能为 null 的值
使用Optional封装map的返回值
```java
Optional<Object> value = Optional.ofNullable(map.get("key"));
```

## 异常与 Optional 的对比

由于某种原因，函数无法返回某个值，这时除了返回null，Java API比较常见的替代做法是抛出一个异常。
```java
public static Optional<Integer> stringToInt(String s){
    try {
        return Optional.of(Integer.parseInt(s));
    } catch (NumberFormatException e) {
        return Optional.empty();
    }
}
```

> 我们的建议是，你可以将多个类似的方法封装到一个工具类中，让我们称之为OptionalUtility。通过这种方式，你以后就能直接调用`OptionalUtility.stringToInt`方法，将String转换为一个`Optional<Integer>`对象，而不再需要记得你在其中封装了笨拙的try/catch的逻辑了。

## 完整示例

```java
package Optional;

import java.util.Optional;
import java.util.Properties;

public class OptionalTest2 {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.setProperty("a", "5");
        props.setProperty("b", "true");
        props.setProperty("e", "false");
        props.setProperty("c", "-3");

        int re = readDuration2(props, "c");
        System.out.println("re = " + re);

    }

    public static int readDuration2(Properties props, String name) {
        return Optional.ofNullable(props.getProperty(name))
                .flatMap(OptionalTest2::stringToInt)
                .filter(i -> i > 0)
                .orElse(0);
    }

    public static int readDuration(Properties props, String name) {
        String value = props.getProperty(name);
        if (value != null) {
            try {
                int i = Integer.parseInt(value);
                if (i > 0) {
                    return i;
                }
            } catch (NumberFormatException ignored) {
            }
        }
        return 0;
    }

    public static Optional<Integer> stringToInt(String s) {
        try {
            return Optional.of(Integer.parseInt(s));
        } catch (NumberFormatException e) {
            return Optional.empty();
        }
    }

}

```
