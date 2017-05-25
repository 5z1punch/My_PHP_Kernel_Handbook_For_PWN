变量声明过程其实就是更新符号表的过程，符号表是一张hashtable，所以不管是变量声明还是赋值，其关键操作都是 zend\_hash\_update 和 zend\_hash\_add 这两个宏，这两个宏其实都是对于\_zend\_hash\_add\_or\_update函数的一个变形，Zend/zend\_hash.h：

```
/* additions/updates/changes */
ZEND_API int _zend_hash_add_or_update(HashTable *ht, const char *arKey, uint nKeyLength, void *pData, uint nDataSize, void **pDest, int flag ZEND_FILE_LINE_DC);
#define zend_hash_update(ht, arKey, nKeyLength, pData, nDataSize, pDest) \
		_zend_hash_add_or_update(ht, arKey, nKeyLength, pData, nDataSize, pDest, HASH_UPDATE ZEND_FILE_LINE_CC)
#define zend_hash_add(ht, arKey, nKeyLength, pData, nDataSize, pDest) \
		_zend_hash_add_or_update(ht, arKey, nKeyLength, pData, nDataSize, pDest, HASH_ADD ZEND_FILE_LINE_CC)q
```

`_zend_hash_add_or_update`  位于Zend/zend_hash.c