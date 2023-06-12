> The Properties class represents **a persistent set **of properties. The Properties can be saved to a **stream** or loaded from a stream. Each key and its corresponding value in the property list is a **string**.
> A property list can contain another property list as its "defaults"; this second property list is searched if the property key is not found in the original property list.
> Because Properties inherits from **Hashtable**, the put and putAll methods can be applied to a Properties object. Their use is strongly discouraged as they allow the caller to insert entries whose keys or values are not Strings. The setProperty method should be used instead. If the store or save method is called on a "compromised" Properties object that contains a non-String key or value, the call will fail. Similarly, the call to the propertyNames or list method will fail if it is called on a "compromised" Properties object that contains a non-String key.

说人话: 一个持久的属性集，属性列表以key-value的形式存在;经常用来读取配置文件

# 特点

- `Hashtable<Object,Object>`的子类
- 可持久属性集
- 保存在流中或从流中加载
- 每个键和对应的值都是一个字符串（String类型）

# 像map一样使用
```java
//Properties作为map集合的使用
private static void PropertiesMap() {
	Properties pro = new Properties();//不需要加泛型，所有的键值都是object类型
	
	//存储元素
	pro.put(001, "张三");
	pro.put(002, "李四");
	pro.put(003, "王五");
	
	//遍历集合
	for(Object key : pro.keySet()) {
		System.out.println(key + ":" + pro.get(key));
	}
}
```

# 作为集合使用
```java
/**
 * Properties作为集合的特有方法
 * 		0bject setProperty(String key,String value):设置类合的链和值，都是String类型，底层调用Hashtable方法put
 * 		string getProperty ( String key):使用此属性列表中指定的性搜索属性
 * 		Set<String> stringPropert yNames():从该属性列表中返回一个不可修改的键集，其中键及其对应的值是字符串
 */
private static void SpecialMethod() {
	Properties pro = new Properties();
	
	pro.setProperty("001", "张三");//键值都是String类型
	pro.setProperty("002", "李四");
	pro.setProperty("003", "王五");
		
	System.out.println(pro.getProperty("001"));//根据键搜索属性
	System.out.println(pro.getProperty("0011"));//没有时返回null
	
	System.out.println(pro);
	
	Set<String> s = pro.stringPropertyNames();//返回一个不可修改的键集，其中键及其对应的值是字符串
	System.out.println(s);
	for(String key : s) {
		System.out.println(key + ":" + pro.getProperty(key));
	}
}
```

# 写入文件
```java
//集合中数据保存到文件
Properties pro = new Properties();

pro.setProperty("001", "张三");//键值都是String类型
pro.setProperty("002", "李四");
pro.setProperty("003", "王五");

System.out.println(pro);

FileWriter fw = new FileWriter("Properties.txt");
pro.store(fw,null);//不想添加描述信息就令第二个参数为null

//数据保存为XML格式文件
//storeToXML(OutputStream os, String comment, String encoding)使用指定的编码方式表示此表中包含的所有属性的XML文档
FileOutputStream f1 = new FileOutputStream("PropertiesXML.XML");
pro.storeToXML(f1, null);
```

# 加载文件
```java
//文件中数据加载到集合
Properties prop = new Properties();
	
FileReader fr = new FileReader("Properties.txt");
prop.load(fr);
	
//将此属性列表打印到指定的输出流。此方法对于调试很有用
prop.list(System.out);
//System.out.println(prop);
 
//从内容为XML格式的文件中读取数据加载为集合
FileInputStream f2 = new FileInputStream("PropertiesXML.XML");
prop.loadFromXML(f2);
 
prop.list(System.out);
//System.out.println(prop);
```

# 函数总览API
`getProperty(String key)`
在此属性列表中搜索具有指定键的属性。如果在此属性列表中找不到该键，则会检查默认属性列表及其默认值（递归）。如果未找到该属性，则该方法返回默认值参数。

`list(PrintStream out)`
将此属性列表打印到指定的输出流。此方法对于调试很有用。

`load(InputStream inStream)` 
从输入字节流中读取属性列表（键和元素对）。输入流采用加载（Reader）中指定的简单的面向行的格式，并假定使用ISO 8859-1字符编码;即每个字节是一个Latin1字符。不在Latin1中的字符和某些特殊字符在使用Unicode转义符的键和元素中表示。 此方法返回后，指定的流仍保持打开状态。

`setProperty(String key, String value)` 
调用 Hashtable 的方法 put 。他通过调用基类的put方法来设置 键值对。

`store(OutputStream out, String comments)` 
将此Properties表中的此属性列表（键和元素对）以适合使用load（InputStream）方法加载到Properties表的格式写入输出流。 此Properties方法不会写出此Properties表的defaults表中的属性（如果有）。

`storeToXML(OutputStream os, String comment, String encoding) `
使用指定的编码发出表示此表中包含的所有属性的XML文档。

`stringPropertyNames()` 
返回此属性列表中的一组键，其中键及其对应的值是字符串，如果尚未从主属性列表中找到相同名称的键，则包括默认属性列表中的不同键。键或键不是String类型的属性将被省略。
