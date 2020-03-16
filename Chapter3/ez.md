# EZ配网开发

Smart配网需要网卡支持进入sniffer模式，捕获空气中的所有无线包以获取手机APP广播的ssid,passwd,token配网信息    
具体流程如下：  

<div align=center><img  src = "ez.png"alt="img" style="zoom:150%;"></div>  
开发流程：  
* 确认wifi模块是否支持sniffer模式  
* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_AUTO 配网模式,token 参数填 NULL（很重要）  
* 确认代码中WLAN_DEV和当前设备使用的wifi名称一致（默认wlan0），SDK的demo代码中已经实现了一些对wifi的操作(设置wifi模块模式切换通道等)，如果日志中有某个指令的运行错误请检查当前系统是否包含该命令  
* 确认是否开启了func_Sniffer线程，因为这个线程是处理捕获空气中的配网信息的，捕获的空口包信息送往s_pSnifferCall这个回调处理(这部分由SDK负责解析)  
* 当 SDK 判断 WiFi 输入的空口包数据正确会调用函数：hwl_wf_wk_mode_set，将 WiFi 状态设置为 station 模式
* 接下来SDK会主动调用hwl_wf_station_connect这个接口，客户需要自己在这个接口实现wifi连接路由器的动作,连接路由器后，确认设备配置好网络并且可以ping通涂鸦的服务器(a2.tuyacn.com)（很重要）  
* 返回设备的真实状态给SDK，hwl_wf_get_ip，hwl_wf_get_mac以及hwl_wf_station_stat_get（很重要),设备所有状态为如下结构体  
```C
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
* 当用户输入了错误的wifi密码的情况下：应该将设备的状态置为WSS_PASSWD_WRONG，并且通过语音或者提示灯提示用户重置设备后重新配网。  
* 注意：请先理解上图的配网流程再进行开发  