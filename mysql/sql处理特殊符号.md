|          |      |       |      |       |       |        |        |
| -------- | ---- | ----- | ---- | ----- | ----- | ------ | ------ |
| 特殊符号 | <    | <=    | >    | >=    | &     | '      | "      |
| 替换符号 | &lt; | &lt;= | &gt; | &gt;= | &amp; | &apos; | &quot; |
|          |      |       |      |       |       |        |        |
|          |      |       |      |       |       |        |        |


# XML 中的 ﹤![CDATA[ ]]>，及其解析

全名: **character data**

所有 XML 文档中的文本均会被解析器解析，除了 CDATA 区段（CDATA section）中的文本会被解析器忽略

CDATA的文本内容中不能出现字符串“`]]>`”。另外，CDATA不能嵌套

XML 实例: 在CDATA标记中的信息被解析器原封不动地传给应用程序，并且不解析该段信息中的任何控制标记。 CDATA区域是由`<![CDATA[`为开始标记，以`]]>`为结束标记，注意CDATA为大写。
