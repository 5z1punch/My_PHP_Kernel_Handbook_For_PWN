* jmp\_buf + 非法时间戳
* 内存缓存块、单链表、伪造pre\_chunk
* gc执行时如果释放对象为OBJECT，则 handlers -&gt; del\_ref ，覆盖del\_ref，会劫持rip。
位置：Zend/zend_variables.c:
```
case IS_OBJECT:
	{
		TSRMLS_FETCH();
		Z_OBJ_HT_P(zvalue)->del_ref(zvalue TSRMLS_CC);
	}
    break;
```
Z\_OBJ\_HT\_P 是对 zval 中的 handlers 取值的宏。



