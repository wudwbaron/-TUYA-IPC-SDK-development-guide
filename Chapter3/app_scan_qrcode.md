# App扫描设备二维码配网

App扫描设备二维码配网,这个配网的应用场景是一些带屏的终端设备，客户可以在屏幕上配置网络（有线和无线均可），并且能够在屏幕上显示二维码     
开发流程：  
* 下载纯有线的4.8.0的SDK（很重要）  
* 确认当前使用的uuid已经录入系统(找涂鸦项目经理帮忙确认)  
* 设备已经配置好网络并且可以ping通涂鸦的服务器(a2.tuyacn.com)  
* 调用：tuya_ipc_set_region接口设置国家码，当前有中国区、美国区、欧洲区可选。该接口在初始化联网模块前调用  
* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_NULL 配网模式,token 参数填 NULL（很重要）  
* 确认tuya_ipc_wired_demo.c 中NET_DEV和当前设备使用的wifi或者有线网卡名称一致（默认eth0）   
* 确认在hwl_bnw_get_ip返回设备的 IP 地址以及hwl_bnw_get_mac返回mac地址  
* 调用tuya_ipc_get_qrcode获取短链接，并将该短链接生成二维码显示在屏幕上  
* app扫描设备上的二维码进行配网  
```C
/**
 * \fn OPERATE_RET tuya_ipc_get_qrcode
 * \brief get qrcode short url from tuya cloud
 * \param[in] appId : which app to use the qrcode
 * \param[in] p_short_url:buf to store the url
 * \param[in] len : the size of p_short_url
 * \return SUCCESS -- OPERATE_RET , FAIL -- COMM ERROR
 */
OPERATE_RET tuya_ipc_get_qrcode(CHAR_T *appId, CHAR_T *p_short_url, INT_T len);
```
* 注意：  
appId参数填NULL时，可用任意涂鸦系列APP配网，若appId不为空，则只允许指定的APP配网，其他APP扫码会跳转到指定APP的下载界面。appId有需要可联系涂鸦项目经理获取。  
