# 有线配网开发

有线配网有两种情况，一种是设备只有有线网卡，一种是设备既可以使用二维码配网又可以使用有线配网
具体流程如下：  
<div align=center><img  src = "wired.png"alt="img" style="zoom:150%;"></div>  
* 一、纯有线配网的流程
  开发流程：  

* 下载纯有线的SDK，因为默认对外输出的SDK为有线加无线或者纯无线的SDK（很重要）

* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_NULL 配网模式,token 参数填 NULL（很重要）   

* 确认在hwl_bnw_get_ip返回设备的 IP 地址以及hwl_bnw_get_mac返回mac地址，再确认获取的ip和app所在同一个局域网(很重要)  

* SDK 将获取到的设备 IP 地址与 Mac 地址通过路由器发送广播包，手机连上路由器 WiFi 并
接收到广播信息后，向服务器获取 token，获取成功后给到设备端进行联网，至此，配网操
作完成。  

* 二、二维码和有线配网同时兼容
  开发流程：  

* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_AUTO 配网模式,token 参数填 NULL（很重要）   

* 确认在hwl_wf_get_ip返回设备的 IP 地址以及hwl_wf_get_mac返回mac地址，再确认获取的ip和app所在同一个局域网(很重要)  

* SDK 将获取到的设备 IP 地址与 Mac 地址通过路由器发送广播包，手机连上路由器 WiFi 并
  接收到广播信息后，向服务器获取 token，获取成功后给到设备端进行联网，至此，配网操
  作完成。  

* 返回设备的真实状态给SDK，hwl_wf_get_ip，hwl_wf_get_mac以及hwl_wf_station_stat_get（很重要),设备所有状态为如下结构体  
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

* 注意：一和二两种配网方式的流程基本一致，不同的是初始化SDK和调用网络状态的接口不同

* [FAQ](https://wudwbaron.github.io/FAQ/connectwifi.html)