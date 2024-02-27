# 简介及作用

解决对象的DTO、DO等对象的转换等问题，Bean映射工具

## 其他Bean映射工具

1. BeanUtils

apache的BeanUtils和spring的BeanUtils 底层都是基于放射实现的Bean映射。而反射的性能是比较低的，因此BeanUtils的性能并不太理想。

1. BeanCopier

cglib的BeanCopier 直接使用ASM在字节码层面编写get/set 方法，然后生成class文件直接执行。由于没有使用反射，BeanCopier 的性能相对于BeanUtils有较大的提升。

1. Dozer

Dozer的底层仍然是基于反射，支持简单属性映射、复杂类型映射、双向映射、隐式映射以及递归映射
