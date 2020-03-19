# 设备日志处理问题

1、怎么设置SDK日志等级？  
设备初始化后调用tuya_ipc_set_log_attr可以设置SDK等级，目前这个接口只能用来设置日志等级，不能设置日志文件保存到指定路径，默认是3即debug等级，范围0-5，等级越高，日志越多  
```C
IPC_APP_Init_SDK(mode, token);
tuya_ipc_set_log_attr(3,NULL);
```
说明：可以设置0-5
2、怎么设置保存日志到本地文件？  
在头文件tuya_ipc_api.h中增加OPERATE_RET tuya_ipc_log_setting(IN CONST INT_T log_level, FILE *filename, BOOL_T if_ms_level) 这个声明，然后设备初始化后调用这个接口去设置保存日志到本地，log是追加的，会自动截断，日志等级默认是3即dbg等级，范围0-5，等级越高，日志越多，  if_ms_level表示开启秒级别打印还是毫秒级，TRUE毫秒级，FALSE为秒级  
tuya_ipc_log_setting 使用示例：  

```C
    IPC_APP_Init_SDK(mode, token);
	FILE *fp = fopen("/tmp/tuya.log", "w");
	if (fp)
	{
		tuya_ipc_log_setting(4, fp, TRUE);
	}
```
说明： 可以设置0-5，串口会根据设置的等级打印日志，如果需要保存日志到本地文件请设置4以上等级；设置4的等级日志保存到tuya.log,日志中是Debug，Err，Notice，Warn的打印；设置5为Debug，Trace的打印
3、怎么把串口输出的完整日志定向到指定文件中？  
在应用启动前，在启动脚本中加入以下指令  

```C
${BINDIR}/${APPNAME} >>/mnt/sdcard/tuya.log 2>&1  
```
${BINDIR}/${APPNAME}表示应用路径


