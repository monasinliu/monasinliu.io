# SparseArray

### 特点

1. 以键值对形式进行存储，基于分查找,因此查找的时间复杂度为0(LogN);
2. 由于SparseArray中Key存储的是数组形式,因此可以直接以int作为Key。避免了HashMap的装箱拆箱操作,性能更高且int的存储开销远远小于Integer;
3. 采用了延迟删除的机制(针对数组的删除扩容开销大的问题的优化， 具体稍后分析) 

### 主要属性

DELETED

Remove方法（Delete）：：remove方法主要做的就是这些，找到需要删除的key，并将对应的value用DELETED替换；但是key仍然存在于mKeys数组，因此删除是一个**伪删除**。这就是所谓的**延迟删除机制**；

