# 二维码配网开发

具体流程如下：  

<div align=center><img  src = "qrcode.png"alt="img" style="zoom:150%;"></div>  
开发流程：  
* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_AUTO 配网模式,token 参数填 NULL（很重要）  

* 确认代码中WLAN_DEV和当前设备使用的wifi名称一致（默认wlan0），SDK的demo代码中已经实现了一些对wifi的操作(设置wifi模块模式切换通道等)，如果日志中有某个指令的运行错误请检查当前系统是否包含该命令  

* 确认在hwl_wf_sniffer_set中是否开启了thread_qrcode线程，SDK中demo代码默认是没有开启这个线程(很重要)  

* 在__tuya_linux_get_snap_qrcode中实现识别app上二维码的动作（<div id = "zbar" > zbar库的使用  </div>），并返回识别的信息给tuya_ipc_direct_connect这个函数（很重要）  

* 如果SDK解析到了正确的配网信息接下来SDK会调用函数：hwl_wf_wk_mode_set，将 WiFi 状态设置为 station 模式

* 接下来SDK会主动调用hwl_wf_station_connect这个接口，客户需要自己在这个接口实现wifi连接路由器的动作（很重要）  

* 连接路由器后，返回设备的真实状态给SDK，hwl_wf_get_ip，hwl_wf_get_mac以及hwl_wf_station_stat_get(很重要),设备所有状态为如下结构体  
```
    typedef enum {
        WSS_IDLE = 0,                       // not connected
        WSS_CONNECTING,                     // connecting wifi
        WSS_PASSWD_WRONG,                   // passwd not match
        WSS_NO_AP_FOUND,                    // ap is not found
        WSS_CONN_FAIL,                      // connect fail
        WSS_CONN_SUCCESS,                   // connect wifi success
        WSS_GOT_IP,                         // get ip success
    }WF_STATION_STAT_E;
```
* 注意：请先理解上图的配网流程再进行开发

* [FAQ](https://wudwbaron.github.io/FAQ/connectwifi.html)