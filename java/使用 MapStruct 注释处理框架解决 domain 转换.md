`BeanUtils.copyProperties()` 和 mapstruct 都是浅拷贝,区别在于两者性能不同,mapstruct 没有使用反射技术，使性能更优。
mapstrut一共两个主要的包，`org.mapstruct.mapstruct`包含里面常用的注释，`org.mapstruct.mapstruct-processor`处理注释的实现。

官网：[MapStruct – Java bean mappings, the easy way!](https://mapstruct.org/)

## 依赖

```xml
<!-- 指定版本 -->
<properties>
	<org.mapstruct.version>1.3.0.Final</org.mapstruct.version>
</properties>

<!-- 引入依赖 -->
<dependencies>
	<!--mapstruct主键-->
	<dependency>
		<groupId>org.mapstruct</groupId>
		<artifactId>mapstruct</artifactId>
		<version>${org.mapstruct.version}</version>
	</dependency>
	<!-- mapstruct实现类执行器 -->
	<dependency>
		<groupId>org.mapstruct</groupId>
		<artifactId>mapstruct-processor</artifactId>
		<version>${org.mapstruct.version}</version>
		<scope>private</scope>
	</dependency>
</dependencies>

```

官方文档还提供了另外一种方式，采用Maven插件形式配置：
```xml
...引用官方文档
...
<properties>
    <org.mapstruct.version>1.2.0.CR1</org.mapstruct.version>
</properties>
...
<dependencies>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct-jdk8</artifactId>
        <version>${org.mapstruct.version}</version>
    </dependency>
</dependencies>
...
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.5.1</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>${org.mapstruct.version}</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
...
```

## 获取Mapper的两种方式

### 默认配置

采用`Mappers`通过动态工厂内部反射机制完成Mapper实现类的获取。
```java
//Mapper接口内部定义
public static GoodInfoMapper MAPPER = Mappers.getMapper(GoodInfoMapper.class);

//外部调用
GoodInfoMapper.MAPPER.from(goodBean,goodTypeBean);
```

### Spring方式配置

`Spring`方式我们需要在`@Mapper`注解内添加`componentModel`属性值，配置后在外部可以采用`@Autowired`方式注入Mapper实现类完成映射方法调用。
```java
//注解配置
@Mapper(componentModel = "spring")
public interface DEMO {}

//注入Mapper实现类
@Autowired
private GoodInfoMapper goodInfoMapper;

//调用
goodInfoMapper.from(goodBean,goodTypeBean);
```

采用了`Spring方式`获取`Mapper`，在自动生成的实现类上`MapStruct`会自动添加`@Component`Spring声明式注入注解配置。

## 基本使用

1. 定义接口，使用`@Mapper`注解（`Mapper`这个定义一般是被广泛应用到`MyBatis`半自动化ORM框架上，而这里的Mapper跟`Mybatis`没有关系。）
2. 定义转换方法，例如方法上常用的注解（如果target和source字段名相同mapstruct会自动copy值）
```java
@Mapping(target="",source="")

@Mappings({
	@Mapping(target="",source=""),
	@Mapping(target="",source="")
})
```

3. `@BeanMapping(ignoreByDefault=true)`忽略自动注解功能

这是一个合同转化为VO的示范

```java
@Mapper  
public interface CompContractConvert {  
   CompContractConvert INSTANCE = Mappers.getMapper(CompContractConvert.class);  
  
   List<CompContractVO> convertToCompContractVO(List<Contract> compContracts);  
  
}
```

## 多个入参

如果多个参数的都有target的属性名需要制定是哪个source的属性名，否则会报错

```java
@Mapper
public interface CarAssertApiMapper {

    CarAssertApiMapper INSTANCE = Mappers.getMapper(CarAssertApiMapper.class);

    /**
     * 两个do转成一个dto
     *
     * @param purchaseBillItemDO 采购明细单
     * @param carManageDO        车型
     * @return 目的dto
     */
    @Mappings({
            @Mapping(target = "modelCode", source = "carManageDO.modelCode"),
            @Mapping(target = "modelName", source = "carManageDO.modelName"),
            @Mapping(target = "modelColor", source = "carManageDO.modelColor"),
            @Mapping(target = "modelColorCode", source = "carManageDO.modelColorCode"),
            @Mapping(target = "interiorColor", source = "carManageDO.interiorColor"),
            @Mapping(target = "interiorColorCode", source = "carManageDO.interiorColorCode"),
            @Mapping(target = "purchaseBillItemId", source = "purchaseBillItemDO.billItemId")
    })
    PurchaseCreateDTO doToDto(PurchaseBillItemDO purchaseBillItemDO, CarManageDO carManageDO);
}

```

## 数据类型转换

MapStruct 在大多数情况下会自动处理类型转换。如原始类型和相应的包装类、原始类型和字符串、枚举类型和字符串、在 BigInt、BigDecimal 和 String 之间等隐式转换。

### 数字格式化
```java
@Mapping(source = "price", target = "price", numberFormat = "$#.00") 
Car getModelFromEntity(CarEntity carEntity); 
```

### 时间格式化

```java
@Mapping(source = "createDate", target = "createDateTime", dateFormat = "yyyy-MM-dd")  
SysLogVO dataConvert(SysLog sysLog);
```

### 自定义表达式

```java
@Mapping(target = "createDateTime", expression = "java(getDate(sysLog.getCreateDate()))")  
SysLogVO dataExpressionConvert(SysLog sysLog);  
  
default String getDate(Date date) {  
    SimpleDateFormat sdf = new SimpleDateFormat("dd.MM-yyyy");  
    return sdf.format(date);  
}
```

如果方法存在于不同的类中，使用 `new class-name.target-method()`

### 常量映射到属性

```java
@Mapping(target = "brand", constant = "BMW") 
Car getModelFromEntity(CarEntity carEntity);
```

### 默认值

defaultValue 属性在source为null的情况下传递默认值
```java
@Mapping(target = "target-property", source="source-property" defaultValue = "default-value")
```

### 默认计算表达式

defaultExpression 传递一个计算值，以防源属性为空；如果 `source-property` 为 `null`，则 `target-property` 将设置为 `default-value-method` 的结果
```java
@Mapping(source = "name", target = "name", defaultExpression = "java(UUID.randomUUID().toString())")
```



## IntelliJ IDEA插件

[MapStruct Support - IntelliJ IDEs Plugin | Marketplace](https://plugins.jetbrains.com/plugin/10036-mapstruct-support)

![](https://mapstruct.org/images/idea/source-auto-complete.gif)

