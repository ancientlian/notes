## Log

```text
logger.info("$METHOD_PAXKAGE$-->$METHOD_NAME$::$PLACE_HOLDERS$",$ARGUMENTS$);
```

参数

```text
PLACE_HOLDERS = groovyScript("_1.collect { it + ' = [{}]'}.join(', ') ", methodParameters())
ARGUMENTS = groovyScript("_1.collect { it }.join(', ') ", methodParameters())
METHOD_NAME = methodName()
METHOD_PAXKAGE = currentPackage()
```
