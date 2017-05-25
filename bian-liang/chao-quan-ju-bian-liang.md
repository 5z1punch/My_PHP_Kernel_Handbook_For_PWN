超全局变量会在所有php代码解释前进行初始化，其初始化工作在 main/php_variables.c中的 `php_startup_auto_globals` 中进行。
例如 `php_auto_globals_create_get` 函数会注册$\_GET变量，该函数调用`zend_hash_update`更新全局符号表，在 `EG(symbol_table)`中插入$\_GET。