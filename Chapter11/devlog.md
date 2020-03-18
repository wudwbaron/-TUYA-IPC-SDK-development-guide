# 设备日志处理问题

1、怎么设置SDK日志等级？  
设备初始化后调用tuya_ipc_set_log_attr可以设置SDK等级，目前这个接口只能用来设置日志等级，不能设置日志文件保存到指定路径，默认是3即debug等级，范围0-5，等级越高，日志越多  
2、怎么设置保存日志到本地文件？  
在头文件增加OPERATE_RET tuya_ipc_log_setting(IN CONST INT_T log_level, FILE *filename, BOOL_T if_ms_level) 这个声明，然后调用这个接口去设置保存日志到本地，log是追加的，会自动截断，日志等级默认是3即debug等级，范围0-5，等级越高，日志越多，  if_ms_level这个参数表示是否开启ms级的时间戳   