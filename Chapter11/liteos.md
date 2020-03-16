# 低功耗SDK问题

1、低功耗设备唤醒出图时间怎么优化？  
(1)mqtt 上线回调函数：__IPC_APP_Get_Net_Status_cb 可以使用 STAT_MQTT_ONLINE
判定 mqtt 是否上线成功，成功后可往下执行剩余操作   
(2)wifi 连通外网后，可另起线程，进行 P2P 初始化，提高设备接入外网效率  
(3)在tuya_ipc_ring_buffer_init中实现强制出I帧的回调函数requestIframeCB  

2、liteos的SDK在应用过程中出现崩溃改如何定位问题  
根据整个压栈过程的lr地址去应用对应的反汇编文件找最终在哪个函数中崩溃  

