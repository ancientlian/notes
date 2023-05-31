
## 示例
``` java
String billStartTime = bills.stream().map(Bill::getBillStartTime).min(String::compareTo)  
       .orElseThrow(() -> ApplicationException.custom_action(-2000, "账单开始时间有误"));
       
double billAmount = bills.stream()  
      .filter(b -> b.getBillStatus().equals("0"))  
      .mapToDouble(b -> Double.parseDouble(b.getBillAmount())).sum();
```

## 说明

对于每行使用流处理的代码，都会创建一个新的 Stream 流对象。这是因为 Java 中的流式处理是基于管道的，每个流管道只能被消费一次，流式处理完毕后就会被关闭。因此每次需要处理流中的数据时，都需要创建一个新的 Stream 流对象。

在代码中多次创建流对象不会带来性能问题，因为在创建流时不会进行实际的数据操作，只是对数据源进行索引和包装。实际的数据操作会在收集器（例如 sum 或 collect 方法）被调用时才进行，而不是每次调用流操作时都进行。

需要注意的是，如果对同一个流进行多次操作，在每次操作后都会创建一个新的流对象，可能会带来额外的性能开销。为了避免这种情况，可以使用 Stream 对象的 peek 方法进行调试，或者使用尽早终止的流操作（例如 findFirst、findAny、limit、anyMatch、allMatch、noneMatch 等方法）。这样可以在不创建新的流对象的情况下操作流中的数据。
