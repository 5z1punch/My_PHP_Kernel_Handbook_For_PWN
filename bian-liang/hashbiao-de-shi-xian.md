# php 中的hashtable的实现

php中的大部分语法特性都是基于hash表来实现的，hash表没有固定的实现方法，各种语言中的hash算法都有所不同，PHP中的哈希表实现在Zend/zend\_hash.c中。



例如对于一个超全局变量的一维数组（如$_GET）做取值操作的话，可以表示为
```
*(zval *)(*(*(HashTable *)(*(zval *)executor_globals->symbol_table->arBuckets[4]->pDataPtr)->value->ht)->arBuckets[5])->pDataPtr
```
其中 executor\_globals->symbol\_table 为全局符号表，4为$\_GET在符号表中的位置，pDataPtr存储了get数组，同样的，php中使用hashtable存储数组，所以该zval的ht属性将指向其变量内容，接下来就是在$\_GET的hashtable中定位相应的键名的下标，此处的5应该是'x'这个键名所计算出的下标，也就是$\_GET['x']，以此递归，直到得到最终值。 