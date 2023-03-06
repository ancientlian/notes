# Lombok

> Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.
> Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.

为什么maven中加入lombok依赖后，还需要安装插件?

因为lombok的引入使得java文件使用javac编译成字节码文件中包含get set函数,但是源代码中找不到定义，IDE会认为这是错误，因此需要安装一个lombok的插件

## 安装步骤

1、IDEA添加插件

2、Maven添加依赖

```xml
<!-- https://mvnrepository.com/artifact/org.projectlombok/lombok -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.18</version>
    <scope>provided</scope>
</dependency>
```

## 常用方法

### @Date

> `@Data` is a convenient shortcut annotation that bundles the features of [`@ToString`](https://projectlombok.org/features/ToString), [`@EqualsAndHashCode`](https://projectlombok.org/features/EqualsAndHashCode), [`@Getter` / `@Setter`](https://projectlombok.org/features/GetterSetter) and [`@RequiredArgsConstructor`](https://projectlombok.org/features/constructor) together

@Data注解在类上，会为类的所有属性自动生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法。

```java
import lombok.AccessLevel;
import lombok.Setter;
import lombok.Data;
import lombok.ToString;

@Data public class DataExample {
  private final String name;
  @Setter(AccessLevel.PACKAGE) private int age;
  private double score;
  private String[] tags;
  
  @ToString(includeFieldNames=true)
  @Data(staticConstructor="of")
  public static class Exercise<T> {
    private final String name;
    private final T value;
  }
}
```

Vanilla Java

```java
import java.util.Arrays;

public class DataExample {
  private final String name;
  private int age;
  private double score;
  private String[] tags;
  
  public DataExample(String name) {
    this.name = name;
  }
  
  public String getName() {
    return this.name;
  }
  
  void setAge(int age) {
    this.age = age;
  }
  
  public int getAge() {
    return this.age;
  }
  
  public void setScore(double score) {
    this.score = score;
  }
  
  public double getScore() {
    return this.score;
  }
  
  public String[] getTags() {
    return this.tags;
  }
  
  public void setTags(String[] tags) {
    this.tags = tags;
  }
  
  @Override public String toString() {
    return "DataExample(" + this.getName() + ", " + this.getAge() + ", " + this.getScore() + ", " + Arrays.deepToString(this.getTags()) + ")";
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof DataExample;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof DataExample)) return false;
    DataExample other = (DataExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (this.getAge() != other.getAge()) return false;
    if (Double.compare(this.getScore(), other.getScore()) != 0) return false;
    if (!Arrays.deepEquals(this.getTags(), other.getTags())) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.getScore());
    result = (result*PRIME) + (this.getName() == null ? 43 : this.getName().hashCode());
    result = (result*PRIME) + this.getAge();
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.getTags());
    return result;
  }
  
  public static class Exercise<T> {
    private final String name;
    private final T value;
    
    private Exercise(String name, T value) {
      this.name = name;
      this.value = value;
    }
    
    public static <T> Exercise<T> of(String name, T value) {
      return new Exercise<T>(name, value);
    }
    
    public String getName() {
      return this.name;
    }
    
    public T getValue() {
      return this.value;
    }
    
    @Override public String toString() {
      return "Exercise(name=" + this.getName() + ", value=" + this.getValue() + ")";
    }
    
    protected boolean canEqual(Object other) {
      return other instanceof Exercise;
    }
    
    @Override public boolean equals(Object o) {
      if (o == this) return true;
      if (!(o instanceof Exercise)) return false;
      Exercise<?> other = (Exercise<?>) o;
      if (!other.canEqual((Object)this)) return false;
      if (this.getName() == null ? other.getValue() != null : !this.getName().equals(other.getName())) return false;
      if (this.getValue() == null ? other.getValue() != null : !this.getValue().equals(other.getValue())) return false;
      return true;
    }
    
    @Override public int hashCode() {
      final int PRIME = 59;
      int result = 1;
      result = (result*PRIME) + (this.getName() == null ? 43 : this.getName().hashCode());
      result = (result*PRIME) + (this.getValue() == null ? 43 : this.getValue().hashCode());
      return result;
    }
  }
}
```

### @Getter/@Setter

> You can annotate any field with `@Getter` and/or `@Setter`, to let lombok generate the default getter/setter automatically.

如果觉得@Data太过残暴（因为@Data集合了@ToString、@EqualsAndHashCode、@Getter/@Setter、@RequiredArgsConstructor的所有特性）不够精细，可以使用@Getter/@Setter注解，此注解在属性上，可以为相应的属性自动生成Getter/Setter方法

```java
import lombok.AccessLevel;
import lombok.Getter;
import lombok.Setter;

public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   * 
   * @param age New value for this person's age. Sky is blue.
   * @return The current value of this person's age. Circles are round.
   */
  @Getter @Setter private int age = 10;
  
  /**
   * Name of the person.
   * -- SETTER --
   * Changes the name of this person.
   * 
   * @param name The new value.
   */
  @Setter(AccessLevel.PROTECTED) private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
}
```

vanilla java

```java
public class GetterSetterExample {
  /**
   * Age of the person. Water is wet.
   */
  private int age = 10;

  /**
   * Name of the person.
   */
  private String name;
  
  @Override public String toString() {
    return String.format("%s (age: %d)", name, age);
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @return The current value of this person's age. Circles are round.
   */
  public int getAge() {
    return age;
  }
  
  /**
   * Age of the person. Water is wet.
   *
   * @param age New value for this person's age. Sky is blue.
   */
  public void setAge(int age) {
    this.age = age;
  }
  
  /**
   * Changes the name of this person.
   *
   * @param name The new value.
   */
  protected void setName(String name) {
    this.name = name;
  }
}
```

### @ToString

> By default, it'll print your class name, along with each field, in order, separated by commas.

类使用@ToString注解，Lombok会生成一个toString()方法，默认情况下会输出类名、所有属性（会按照属性定义顺序），用逗号来分割。

> By setting the `includeFieldNames` parameter to *true* you can add some clarity (but also quite some length) to the output of the `toString()` method.

通过将`includeFieldNames`参数设为true，就能明确的输出toString()属性

> By default, all non-static fields will be printed. If you want to skip some fields, you can annotate these fields with `@ToString.Exclude`. Alternatively, you can specify exactly which fields you wish to be used by using `@ToString(onlyExplicitlyIncluded = true)`, then marking each field you want to include with `@ToString.Include`.

`@ToString.Exclude`：指定跳过的字段

`@ToString(onlyExplicitlyIncluded = true)`和`@ToString.Include`：只使用指定的字段

> By setting `callSuper` to *true*, you can include the output of the superclass implementation of `toString` to the output. Be aware that the default implementation of `toString()` in `java.lang.Object` is pretty much meaningless, so you probably don't want to do this unless you are extending another class.

`callSuper`：将toString的超类实现的输出包含到输出中。

> You can also include the output of a method call in your `toString`. Only instance (non-static) methods that take no arguments can be included. To do so, mark the method with `@ToString.Include`.

您还可以在toString中包含方法调用的输出。只能包含不带参数的实例(非静态)方法。为此，用`@ToString.Include`标记该方法。

> You can change the name used to identify the member with `@ToString.Include(name = "some other name")`, and you can change the order in which the members are printed via `@ToString.Include(rank = -1)`. Members without a rank are considered to have rank 0, members of a higher rank are printed first, and members of the same rank are printed in the same order they appear in the source file.

 `@ToString.Include(name = "some other name")`:额外指定命名

`@ToString.Include(rank = -1)`：按优先级改变输出顺序，从高到低，默认为0，等级相同按源文件顺序。

示例：

```java
import lombok.ToString;

@ToString
public class ToStringExample {
  private static final int STATIC_VAR = 10;
  private String name;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  @ToString.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
  
  @ToString(callSuper=true, includeFieldNames=true)
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
  }
}
```

Vanilla Java

```java
import java.util.Arrays;

public class ToStringExample {
  private static final int STATIC_VAR = 10;
  private String name;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
    
    @Override public String toString() {
      return "Square(super=" + super.toString() + ", width=" + this.width + ", height=" + this.height + ")";
    }
  }
  
  @Override public String toString() {
    return "ToStringExample(" + this.getName() + ", " + this.shape + ", " + Arrays.deepToString(this.tags) + ")";
  }
}
```

### @EqualsAndHashCode

With Lombok

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  @EqualsAndHashCode.Exclude private Shape shape = new Square(5, 10);
  private String[] tags;
  @EqualsAndHashCode.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
  
  @EqualsAndHashCode(callSuper=true)
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
  }
}

```

Vanilla Java

```java
import java.util.Arrays;

public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private Shape shape = new Square(5, 10);
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof EqualsAndHashCodeExample)) return false;
    EqualsAndHashCodeExample other = (EqualsAndHashCodeExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (Double.compare(this.score, other.score) != 0) return false;
    if (!Arrays.deepEquals(this.tags, other.tags)) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.score);
    result = (result*PRIME) + (this.name == null ? 43 : this.name.hashCode());
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.tags);
    return result;
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof EqualsAndHashCodeExample;
  }
  
  public static class Square extends Shape {
    private final int width, height;
    
    public Square(int width, int height) {
      this.width = width;
      this.height = height;
    }
    
    @Override public boolean equals(Object o) {
      if (o == this) return true;
      if (!(o instanceof Square)) return false;
      Square other = (Square) o;
      if (!other.canEqual((Object)this)) return false;
      if (!super.equals(o)) return false;
      if (this.width != other.width) return false;
      if (this.height != other.height) return false;
      return true;
    }
    
    @Override public int hashCode() {
      final int PRIME = 59;
      int result = 1;
      result = (result*PRIME) + super.hashCode();
      result = (result*PRIME) + this.width;
      result = (result*PRIME) + this.height;
      return result;
    }
    
    protected boolean canEqual(Object other) {
      return other instanceof Square;
    }
  }
}
```

### @NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor

With Lombok

```java
import lombok.AccessLevel;
import lombok.RequiredArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.NonNull;

@RequiredArgsConstructor(staticName = "of")
@AllArgsConstructor(access = AccessLevel.PROTECTED)
public class ConstructorExample<T> {
  private int x, y;
  @NonNull private T description;
  
  @NoArgsConstructor
  public static class NoArgsExample {
    @NonNull private String field;
  }
}
```

Vanilla Java

```java
public class ConstructorExample<T> {
  private int x, y;
  @NonNull private T description;
  
  private ConstructorExample(T description) {
    if (description == null) throw new NullPointerException("description");
    this.description = description;
  }
  
  public static <T> ConstructorExample<T> of(T description) {
    return new ConstructorExample<T>(description);
  }
  
  @java.beans.ConstructorProperties({"x", "y", "description"})
  protected ConstructorExample(int x, int y, T description) {
    if (description == null) throw new NullPointerException("description");
    this.x = x;
    this.y = y;
    this.description = description;
  }
  
  public static class NoArgsExample {
    @NonNull private String field;
    
    public NoArgsExample() {
    }
  }
}
```

## 参考

<https://www.cnblogs.com/heyonggang/p/8638374.html>

<https://projectlombok.org/features/all>

> 总结
>
> Lombok虽然有很多优点，但Lombok更类似于一种IDE插件，项目也需要依赖相应的jar包。Lombok依赖jar包是因为编译时要用它的注解，为什么说它又类似插件？因为在使用时，eclipse或IntelliJ IDEA都需要安装相应的插件，在编译器编译时通过操作AST（抽象语法树）改变字节码生成，变向的就是说它在改变java语法。它不像spring的依赖注入或者mybatis的ORM一样是运行时的特性，而是编译时的特性。这里我个人最感觉不爽的地方就是对插件的依赖！因为Lombok只是省去了一些人工生成代码的麻烦，但IDE都有快捷键来协助生成getter/setter等方法，也非常方便。
>
> 知乎上有位大神发表过对Lombok的一些看法：
>
> ```text
> 这是一种低级趣味的插件，不建议使用。JAVA发展到今天，各种插件层出不穷，如何甄别各种插件的优劣？能从架构上优化你的设计的，能提高应用程序性能的 ，
> 实现高度封装可扩展的...， 像lombok这种，像这种插件，已经不仅仅是插件了，改变了你如何编写源码，事实上，少去了代码你写上去又如何？ 
> 如果JAVA家族到处充斥这样的东西，那只不过是一坨披着金属颜色的屎，迟早会被其它的语言取代。
> ```
>
> 虽然话糙但理确实不糙，试想一个项目有非常多类似Lombok这样的插件，个人觉得真的会极大的降低阅读源代码的舒适度。
>
> 虽然非常不建议在属性的getter/setter写一些业务代码，但在多年项目的实战中，有时通过给getter/setter加一点点业务代码，能极大的简化某些业务场景的代码。所谓取舍，也许就是这时的舍弃一定的规范，取得极大的方便。
>
> 我现在非常坚信一条理念，任何编程语言或插件，都仅仅只是工具而已，即使工具再强大也在于用的人，就如同小米加步枪照样能赢飞机大炮的道理一样。结合具体业务场景和项目实际情况，无需一味追求高大上的技术，适合的才是王道。
>
> Lombok有它的得天独厚的优点，也有它避之不及的缺点，熟知其优缺点，在实战中灵活运用才是王道。
