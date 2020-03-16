# App扫描设备二维码配网

App扫描设备二维码配网,这个配网的应用场景是一些带屏的终端设备，客户可以在屏幕上配置网络（有线和无线均可），并且能够在屏幕上显示二维码     
开发流程：  
* 下载纯有线的4.8.0的SDK（很重要）  
* 确认当前使用的uuid已经录入系统(找涂鸦项目经理帮忙确认)
* 设备已经配置好网络并且可以ping通涂鸦的服务器(a2.tuyacn.com)
* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_NULL 配网模式,token 参数填 NULL（很重要）  
* 确认tuya_ipc_wired_demo.c 中NET_DEV和当前设备使用的wifi或者有线网卡名称一致（默认eth0） 
* 确认在hwl_bnw_get_ip返回设备的 IP 地址以及hwl_bnw_get_mac返回mac地址  
* 调用tuya_ipc_get_qrcode获取短链接，并将该短链接生成二维码显示在屏幕上
* app扫描设备上的二维码进行配网  

