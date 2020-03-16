# 录像问题  

1、录像功能开发流程是什么？  
调用 TUYA_APP_Init_Stream_Storage 函数进行本地存储初始化，默认开启连续存储，如需开
启事件存储，开启 tuya_ipc_ss_set_write_mode(SS_WRITE_MODE_EVENT)   

2、本地事件录像功能开发流程是什么，开发过程中需要注意什么？  
调用函数:tuya_ipc_ss_start_event开启录像，调用:tuya_ipc_ss_stop_event 关闭录像，单个事件录像最长时间 10分钟，超过 10 分钟若没有调用:tuya_ipc_ss_stop_event,则 SDK 会自动关闭录像   
3、sd卡满了录像文件怎么处理？
但sd开存储空间小于DISK_CLEAN_THRESH_KB的值(默认是100M),则覆盖旧的录像文件  

4、本地录像功能开发的时间在如何获取？  
在调用初始化函数 TUYA_APP_Init_Stream_Storage之前应该先调用tuya_ipc_get_service_time同步时间到设备本地，否则会造成录像文件异常  



