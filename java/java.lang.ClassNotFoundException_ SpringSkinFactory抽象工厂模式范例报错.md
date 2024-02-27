示例代码：
```java
package AbstractFactory.utils;

import javax.xml.parsers.*;

import org.w3c.dom.*;

import java.io.*;

public class XMLUtil {
    //该方法用于从XML配置文件中提取具体类类名，并返回一个实例对象
    public static Object getBean() {
        try {
            //创建文档对象
            DocumentBuilderFactory dFactory = DocumentBuilderFactory.newInstance();
            DocumentBuilder builder = dFactory.newDocumentBuilder();
            Document doc;
            doc = builder.parse(new File("src/AbstractFactory/config/config.xml"));
            //获取包含类名的文本节点
            NodeList nl = doc.getElementsByTagName("className");
            Node classNode = nl.item(0).getFirstChild();
            String cName = classNode.getNodeValue();
            //通过类名生成实例对象并将其返回
            Class<?> c = Class.forName(cName);
//            Class<?> c = Class.forName("AbstractFactory.factory." +cName);
            Object obj = c.newInstance();
            return obj;
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
    }
}
```

报错：

```java
java.lang.ClassNotFoundException: SpringSkinFactory
	at java.net.URLClassLoader.findClass(URLClassLoader.java:387)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:418)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:355)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:351)
	at java.lang.Class.forName0(Native Method)
	at java.lang.Class.forName(Class.java:264)
	at AbstractFactory.utils.XMLUtil.getBean(XMLUtil.java:23)
	at AbstractFactory.Client.main(Client.java:16)
Exception in thread "main" java.lang.NullPointerException
	at AbstractFactory.Client.main(Client.java:18)
```

原因：

缺少包名，原例的代码是在默认的包下面，需要加上路径

如我的路径
```java
Class<?> c = Class.forName("AbstractFactory.factory." + cName);
```
