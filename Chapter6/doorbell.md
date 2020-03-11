# 低功耗休眠功能开发
* 开发流程：  
1、使用 tuya_ipc_get_client_conn_info这个接口判断目前设备是否有人在预览，如果没有的话进入休眠状态  

```C    
/**
 * \fn OPERATE_RET tuya_ipc_get_client_conn_info(OUT UINT_T *p_client_num, OUT CLIENT_CONNECT_INFO_S **p_p_conn_info)
 * \brief get P2P connection information
 * \param[out] p_client_num: current connected client number
 * \param[out] p_p_conn_info
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_get_client_conn_info(OUT UINT_T *p_client_num, OUT CLIENT_CONNECT_INFO_S **p_p_conn_info);  
```

2、调用tuya_ipc_dp_report上报149dp点状态为0设备进入休眠状态  
3、调用函数：tuya_ipc_book_wakeup_topic 通知服务器，设备将要准备进入睡眠状态  
4、 通过：tuya_ipc_get_mqtt_socket_fd 获取对应的 socket fd 信息  
5、通过：tuya_ipc_get_wakeup_data 获取 app 唤醒的数据，设备低功耗芯片与服务器取得
通信后，判断是否有接收到唤醒数据来唤醒设备  
6、通过：tuya_ipc_get_heartbeat_data 获取设备休眠时，与服务器建立连接的保活包，设备休眠时，保活包的发送频率建议为 30S~120S(liteos只需要将socket fd和wakeup data送入自带的休眠接口即可，这一步可以不需要)  
7、 设备进入休眠状态，只留下低功耗芯片与网络保持心跳包通信  
8、以下为设备休眠前的案例代码  
```C  
OPERATE_RET TUYA_APP_LOW_POWER_ENABLE()
{
    PR_DEBUG("low power en");
    BOOL_T  doorStat = FALSE;
    OPERATE_RET ret = 0;
    //Report sleep status to tuya
    ret = tuya_ipc_dp_report(NULL, TUYA_DP_DOOR_STATUS,PROP_BOOL,&doorStat,1);
    if (OPRT_OK != ret){
        //This dp point is very important. The user should repeat call the report interface until the report is successful when it is fail.
        //If it is failure continues, the user needs to check the network connection.
        PR_ERR("dp report failed");
        return ret;
    }
    ret = tuya_ipc_book_wakeup_topic();
    if (OPRT_OK != ret){
        PR_ERR("tuya_ipc_book_wakeup_topic failed");
        return ret;            
    }
    ret = tuya_ipc_get_wakeup_data(s_wakeup_data, &s_wakeup_len);
    if (OPRT_OK != ret){
        PR_ERR("tuya_ipc_get_wakeup_data failed");
        return ret;   
    }
    
    int i = 0;
    
    for(i = 0; i < s_wakeup_len; i++) {
        printf("%x ", s_wakeup_data[i]);
    }
    
    printf("\n");
    
    //Get fd for server to wakeup
    s_wakeup_fd =  tuya_ipc_get_mqtt_socket_fd();
    if (-1 == s_wakeup_fd){
        PR_ERR("tuya_ipc_get_mqtt_socket_fd failed");
        return ret; 
    }
    
    //Create a sock receive thread and receive the wakeup package
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setstacksize(&attr, 1024 * 1024);
    s_wake_task_stat = TRUE;
    ret = pthread_create(&s_wake_send_pthread, &attr, __wakeup_task, NULL);
    if (OPRT_OK != ret){
        PR_ERR("task create failed");
        s_wake_task_stat = FALSE;
        return ret;
    }
    pthread_attr_destroy(&attr);
    
    return ret;
}

```
# 低功耗设备唤醒功能开发  
* 开发流程：  
1、设备端收到唤醒包，且比对唤醒数据无误时，设备进入唤醒状态  
2、设备无需做重新连接wifi的动作  
3、创建新的P2P的初始化线程可以和SDK初始化同步进行，加快唤醒出图时间（注意第一次配网需要放在mqtt上线后再进行p2p的初始化）  

# 低功耗设备功能启动优化  
* mqtt 上线回调函数：__IPC_APP_Get_Net_Status_cb 可以使用 STAT_MQTT_ONLINE
判定 mqtt 是否上线成功，成功后可往下执行剩余操作  
* wifi 连通外网后，可另起线程，进行 P2P 初始化，提高设备接入外网效率  
* 设备只需检测到 SD 卡挂载成功，即可进行本地录像初始化，SDK 带有时间修复机制，会自
动检测并修复时间戳异常的数据  
* 带有 Echo 与 Chromecast 增值服务的设备，Echo 或 Chromecast 启动时，会调用对应的回
调接口 TUYA_APP_Echoshow_Start 或 TUYA_APP_Chromecast_Start，设备收到 start 回调时
不能休眠，直到收到停止回调 TUYA_APP_Echoshow_Stop 或 TUYA_APP_Chromecast_Stop
时才能休眠  
*  使用本地存储事件录像功能，调用函数：tuya_ipc_ss_stop_event 后，设备需要稍等 2~3 秒，
直至录像数据已经保存至 SD 卡再进入休眠  
* 使用云存储功能，调用函数 tuya_ipc_cloud_storage_event_delete 后，需要调用：
tuya_ipc_cloud_storage_get_event_status_by_id 查询云存储数据上传的状态，查询状态不为：
EVENT_ONGOING 或 EVENT_READY 时，设备才可进入休眠状态，否则容易出现云存储数据
丢失的情况，具体说明如下代码所示：  
```C
typedef enum
{
    EVENT_NONE,
    EVENT_ONGOING,
    EVENT_READY,
    EVENT_INVALID
}EVENT_STATUS_E;
```
















