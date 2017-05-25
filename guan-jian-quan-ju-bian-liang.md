* <executor_globals+392> 存放着 `executor_globals->symbol_table` 是全局符号表，使用`EG(symbol_table)`调用。
* gc_globals 存放着与gc相关的数据，包括root\_buffer的地址等等，该变量在垃圾回收中至关重要。
* execl 在 got 表中的地址：
```
In [45]: hex(ELF('libphp5.so').got['execl'])
Out[45]: '0xa4b958'
```
另外，got中没有sysytem。
execl可算libc基址，execl加载的真实地址存储在 `got['execl']+php基址` 这个位置上。
```
execl_addr = u64(read_memory(execl_got+libphp_base,8))
```
* std\_object\_handles uaf的利用中通常用于泄漏合法的php中的内存地址，方便算基址和偏移。object对象的zval头部为 <&std\_object\_handles>+\00\00\00\00\05\00 （最后的这四个连续的\00其实是zval中的refcount\_\_gc，已被释放的对象为00。~~还被引用着的对象好像是从第二个字节开始计数的？例如\00\01\00\00？我可能记错了，因为这不和逻辑，以后再改这个地方吧~~**TODO**）