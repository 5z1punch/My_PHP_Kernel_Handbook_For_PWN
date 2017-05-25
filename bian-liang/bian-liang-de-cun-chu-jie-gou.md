# Zval 结构体

变量的值存储在名为zval的结构体中，定义位置在Zend/zend.h中：

```c
typedef struct _zval_struct zval;
//...
struct _zval_struct {
    /* Variable information */
    zvalue_value value;     /* value */
    zend_uint refcount__gc;
    zend_uchar type;    /* active type */
    zend_uchar is_ref__gc;
};
```

所有的php变量都会使用该结构体进行存储，zval中四个字段的含义分别为

