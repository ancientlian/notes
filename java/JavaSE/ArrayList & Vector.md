ArrayList是非线程安全，而Vector是线程安全的，那么表现在源码上是怎么样的区别呢？就是在每个ArrayList的方法前，加上`synchronized`。

```java
// Vector的add方法
public synchronized boolean add(E e) {
    modCount++;
    ensureCapacityHelper(elementCount + 1);
    elementData[elementCount++] = e;
    return true;
}
// ArrayList的add方法
public boolean add(E e) {
    ensureCapacityInternal(size + 1);
	elementData[size++] = e;
	return true;
}
```

ArrayList和Vector都具有动态扩容的特性，唯一的区别是，ArrayList扩容后是原来的1.5倍。Vector中有一个capacityIncrement变量，每次扩容都在原来大小基础上增加capacityIncrement。如果capacityIncrement==0，那么就在原大小基础上再扩充一倍。

Vector中有一个方法`setSize(int newSize)`，而ArrayList并没有，这个方法有点鸡肋。
>  setSize允许用户主动设置容器大小，如果newSize小于当前size，那么elementData数组中只会保留newSize个元素，多出来的会设为null。如果newSize大于当前size，那么就扩容到newSize大小，数组中多出来的部分设为null，以后添加元素的时候，之前多出来的部分就会以null的形式存在，

# 动态扩容
```java
    /**
     * Default initial capacity.
     */
    private static final int DEFAULT_CAPACITY = 10;

    /**
     * Shared empty array instance used for empty instances.
     */
    private static final Object[] EMPTY_ELEMENTDATA = {};

    /**
     * Shared empty array instance used for default sized empty instances. We
     * distinguish this from EMPTY_ELEMENTDATA to know how much to inflate when
     * first element is added.
     */
    private static final Object[] DEFAULTCAPACITY_EMPTY_ELEMENTDATA = {};

    /**
     * The array buffer into which the elements of the ArrayList are stored.
     * The capacity of the ArrayList is the length of this array buffer. Any
     * empty ArrayList with elementData == DEFAULTCAPACITY_EMPTY_ELEMENTDATA
     * will be expanded to DEFAULT_CAPACITY when the first element is added.
     */
    transient Object[] elementData; // non-private to simplify nested class access

    /**
     * The size of the ArrayList (the number of elements it contains).
     *
     * @serial
     */
    private int size;
```
所谓动态扩容，就是当数组中存储的元素达到容量上限以后，ArrayList会创建一个新的数组，新数组的大小为当前数组大小的1.5倍。随后将数组元素拷贝到新数组，如果这个动作频繁执行的话，会增大性能开销。

**当容量达到上限的时候，如何动态扩充数组大小**

```java
public boolean add(E e) {
    // 每次添加元素之前先动态调整数组大小，避免溢出
    ensureCapacityInternal(size + 1);
    // 为什么ArrayList的元素都是顺序存放的？这就是原因，每次都会把最新添加的元素放到数组末尾。
    elementData[size++] = e;
    return true;
}
private void ensureCapacityInternal(int minCapacity) {
    // 如果当前容器为空，那么就先初始化数组，数组大小不能小于DEFAULT_CAPACITY
    if (elementData == EMPTY_ELEMENTDATA) {
        minCapacity = Math.max(DEFAULT_CAPACITY, minCapacity);
    }
    ensureExplicitCapacity(minCapacity);
}
private void ensureExplicitCapacity(int minCapacity) {
    modCount++;
    // 容器会在什么时候扩容？ 就是他了！ 如果当前元素数量达到了容器的上限，那么就扩充数组
    if (minCapacity - elementData.length > 0)
        grow(minCapacity);
}
private void grow(int minCapacity) {
    // oldCapacity为当前容器大小
    int oldCapacity = elementData.length;
    // oldCapacity >> 1和oldCapacity / 2是等效的，因此newCapacity为原来的1.5倍
    int newCapacity = oldCapacity + (oldCapacity >> 1);
    // 因为第一次容器有可能为空，elementData.length==0，newCapacity会小于minCapacity
    if (newCapacity - minCapacity < 0)
        newCapacity = minCapacity;
    // 当然newCapacity也不能大于MAX_ARRAY_SIZE，因为数组能分配的最大空间就是Integer.MAX_VALUE
    if (newCapacity - MAX_ARRAY_SIZE > 0)
        newCapacity = hugeCapacity(minCapacity);
    // 当确定好数组大小后，就可以进行数组拷贝，Arrays.copyOf的底层是一个native方法，后续有机会会讲到他的实现。
    elementData = Arrays.copyOf(elementData, newCapacity);
}
private static int hugeCapacity(int minCapacity) {
    if (minCapacity < 0) // overflow
        throw new OutOfMemoryError();
    return (minCapacity > MAX_ARRAY_SIZE) ?
        Integer.MAX_VALUE :
        MAX_ARRAY_SIZE;
}
```

**当容器满了以后需要扩容，那当容器元素不足1/2或者更少的时候是否需要动态减容呢？**
删除容器中的元素，数组大小并不会因此而减小。

## 添加元素

Add首先都要检查扩容，而`add(int index, E element)`方法中多一步操作，就是将指定位置以后的所有元素向后移动一位，留出当前位置用来存放添加的元素。

## 删除元素

有两个方法removeAll和retainAll他们正好是互斥的两个操作，但是底层都调用了同一个方法来实现

```java
// 删除包含集合C的元素
public boolean removeAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, false);
}
// 除了包含集合C的元素外，一律被删除。也就是说，最后只剩下c中的元素。
public boolean retainAll(Collection<?> c) {
    Objects.requireNonNull(c);
    return batchRemove(c, true);
}
private boolean batchRemove(Collection<?> c, boolean complement) {
    final Object[] elementData = this.elementData;
    int r = 0, w = 0;
    boolean modified = false;
    try {
        for (; r < size; r++)
            // 我认为这里有两点值得我们学习
            // 第一，作者巧妙的提取了逻辑上的最大公约数，仅通过一行逻辑判断就实现了两个互斥的效果。
            // 第二，作者的所用操作都集中于elementData一个数组上，避免了资源的浪费。
            if (c.contains(elementData[r]) == complement)
                elementData[w++] = elementData[r];
    } finally {
        // 理论上r==size ；只有当出现异常情况的时候，才会出现r!=size，一旦出现了异常，
        // 那么务必要将之前被修改过的数组再还原回来。
        if (r != size) {
            System.arraycopy(elementData, r,
                             elementData, w,
                             size - r);
            w += size - r;
        }
        if (w != size) {
            // 被删除部分数组后，剩余的所有元素被移到了0-w之间的位置，w位置以后的元素都被置空回收。
            for (int i = w; i < size; i++)
                elementData[i] = null;
            modCount += size - w;
            size = w;
            modified = true;
        }
    }
    return modified;
}
```
