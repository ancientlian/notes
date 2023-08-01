`BeanUtils.copyProperties()` 和 mapstruct 都是浅拷贝,区别在于两者性能不同,mapstruct 没有使用反射技术，使性能更优。
mapstrut一共两个主要的包，org.mapstruct.mapstruct包含里面常用的注释，org.mapstruct.mapstruct-processor处理注释的实现。

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

## 基本使用

1. 定义接口，使用`@Mapper`注解
2. 定义转换方法，例如方法上常用的注解（如果target和source字段名相同mapstruct会自动copy值）
```java
@Mapping(target="",source="")、
@Mappings({@Mapping(target="",source=""),@Mapping(target="",source="")})
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

MapStruct 在大多数情况下会自动处理类型转换。

如果有一个场景需要进行时间的格式化，那么就要借助表达式


