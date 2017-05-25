变量声明过程其实就是更新符号表的过程，符号表是一张hashtable，所以不管是变量声明还是赋值，其关键操作都是 zend\_hash\_update 和 zend\_hash\_add 这两个宏，这两个宏其实都是对于\_zend\_hash\_add\_or\_update函数的一个变形，Zend/zend\_hash.h：

```
/* additions/updates/changes */
ZEND_API int _zend_hash_add_or_update(HashTable *ht, const char *arKey, uint nKeyLength, void *pData, uint nDataSize, void **pDest, int flag ZEND_FILE_LINE_DC);
#define zend_hash_update(ht, arKey, nKeyLength, pData, nDataSize, pDest) \
		_zend_hash_add_or_update(ht, arKey, nKeyLength, pData, nDataSize, pDest, HASH_UPDATE ZEND_FILE_LINE_CC)
#define zend_hash_add(ht, arKey, nKeyLength, pData, nDataSize, pDest) \
		_zend_hash_add_or_update(ht, arKey, nKeyLength, pData, nDataSize, pDest, HASH_ADD ZEND_FILE_LINE_CC)q
```

`_zend_hash_add_or_update`  位于Zend/zend_hash.c中，其函数原型为：
```
ZEND_API int _zend_hash_add_or_update(HashTable *ht, const char *arKey, uint nKeyLength, void *pData, uint nDataSize, void **pDest, int flag ZEND_FILE_LINE_DC)
```
如果不看其冲突处理算法的话，hash表中的元素定位是靠这两句代码所决定的：

```
h = zend_inline_hash_func(arKey, nKeyLength);
nIndex = h & ht->nTableMask;
```
zend\_inline\_hash\_func 定义于 zend\_hash.h ，我使用python重写了该算法：
php_hashtable.py
```
def zend_inline_hash_func(arKey):
    hash = 5381
    arKey += "\00"
    nKeyLength = len(arKey)
    i = 0
    while nKeyLength>=8:
        for x in range(0,8):
            hash = ((hash << 5) + hash) + ord(arKey[i])
            i+=1
        nKeyLength -= 8
    while nKeyLength > 0:
        hash = ((hash << 5) + hash) + ord(arKey[i])
        i+=1
        nKeyLength-=1
    return hash
```
最终该hash会与hash表的nTableMask属性做`&`操作计算出该变量在hashtable中的位置，这是未考虑冲突的情况。