# Cannot use native queries with dynamic sorting and/or pagination in method public abstract org

在 sql 最后添加 `\n-- #pageable\n` 即可。注意对应的 pageable 参数放在最后。

## 参考

[Site Unreachable](https://stackoverflow.com/questions/38349930/spring-data-and-native-query-with-pagination)
