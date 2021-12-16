

# IDEA下某个module无法build生成target

## 起因

部署web项目的时候并没有成功，说是资源不可访问，于是查找原因

1、首先检查了业务代码，看前端到controller有无问题

2、其次检查了项目的配置

3、后来发现在out输出里面并没有classes编译好的controller

4、回溯到module中发现build并没有产生target文件

5、最后定位于.iml文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<module type="JAVA_MODULE" version="4">
  <component name="FacetManager">
    <facet type="web" name="Web">
      <configuration>
        <descriptors>
          <deploymentDescriptor name="web.xml" url="file://$MODULE_DIR$/web/WEB-INF/web.xml" />
        </descriptors>
        <webroots>
          <root url="file://$MODULE_DIR$/web" relative="/" />
        </webroots>
        <sourceRoots>
          <root url="file://$MODULE_DIR$/src/main/resources" />
          <root url="file://$MODULE_DIR$/src/main/java" />
        </sourceRoots>
      </configuration>
    </facet>
  </component>
  <component name="NewModuleRootManager" inherit-compiler-output="true">
    <exclude-output />
    <content url="file://$MODULE_DIR$" />
    <orderEntry type="inheritedJdk" />
    <orderEntry type="sourceFolder" forTests="false" />
  </component>
</module>
```

## 解决方案

```xml
<component name="NewModuleRootManager" inherit-compiler-output="true">
    <exclude-output />
    <content url="file://$MODULE_DIR$" />
    <orderEntry type="inheritedJdk" />
    <orderEntry type="sourceFolder" forTests="false" />
  </component>
```

这一段是无效的配置，删除即可

## 拓展

iml是 intellij idea的工程配置文件，里面是当前project的一些配置信息。

