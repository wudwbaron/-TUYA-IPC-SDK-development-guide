# 本地回放功能开发
* 涂鸦 SDK 库文件已集成本地存储功能，开发者仅需要将音视频送入 ringbuffer，且完成本地
存储的设置即可。因此，本节将主要介绍如何开发本地存储录像的开启与设置  
* 前面一节已经完成了送入音视频到SDK的流程，所以本节重点在讲解如何开启本地储存功能  
*  同步时间开发：开启本地录像前必须先同步时间，避免时间不正确，找不到回放视频问题。  
*  在 mqtt 上线后，调用 IPC_APP_Sync_Utc_Time 函数与服务器同步时间。  
  如何判断mqtt是否上线：当回调函数__IPC_APP_Get_Net_Status_cb中的stat为STAT_MQTT_ONLINE时，赋值s_mqtt_status为1，即当全局变量s_mqtt_status为1时mqtt为上线状态  
  在IPC_APP_Sync_Utc_Time获取时间并设置时间到设备本地      
```C
    OPERATE_RET IPC_APP_Sync_Utc_Time(VOID)
    {
        TIME_T time_utc;
        INT_T time_zone;
        PR_DEBUG("Get Server Time ");
        OPERATE_RET ret = tuya_ipc_get_service_time_force(&time_utc, &time_zone);

        if(ret != OPRT_OK)
        {
            return ret;
        }
        //The API returns OK, indicating that UTC time has been successfully obtained.
        //If it return not OK, the time has not been fetched.

        PR_DEBUG("Get Server Time Success: %lu %d", time_utc, time_zone);
    //  TODO
    //  Setting the time using the system interface such as settimeofday;
        return OPRT_OK;
        }
```
  关于设备和服务器同步时间的时区：    
  设备同步到的时间为配网时手机的时区信息，如需修改设备时区仅在设备第一次配网前，更改手机自身时区，引导设备配网，新时区会生效（即：若设备已配网成功，修改手机时区，新时区不会生效）。以手机自身的时区时间为准，与账号的区域和 app 显示的时区无关。获取到的时区的值单位为秒比如获取的时区为东八区则获取的值为8*60*60=28800。  

* 调用接口：tuya_ipc_check_in_dls 做夏令时的判断，判断结果为 true，则时区+1  
  夏令时判断范例代码：  
```C  
    TIME_T time_utc;
    tuya_ipc_get_utc_time(&time_utc);
    BOOL_T isDls = FALSE;
    tuya_ipc_check_in_dls(time_utc,&isDls);
    if (TRUE == isDls)
    {
    time_utc += 3600;
    }
```
* 在初始化本地存储之前要先确认sd卡的状态：SDK会调用tuya_ipc_sd_get_status 来获取SD卡的状态，需要确认返回的状态为SD_STATUS_NORMAL，本地存储才会初始化成功，如以下日志report sd status 0 -> 2:表示sd卡状态异常导致本地存储初始化异常   

```C
    [03-11 10:58:25-- TUYA Debug][tuya_ipc_stream_storage.c:1242] report sd status 0 -> 2

    [03-11 10:58:25-- TUYA Debug][tuya_iot_com_api.c:633] dp compo:{"110":2}
    [03-11 10:58:25-- TUYA Debug][smart_frame.c:1536] dp<110> check. need_update:1 pv_stat:0 trig_t:0 filter_type:0 force_send:1
    [03-11 10:58:25-- TUYA Debug][smart_frame.c:808] first serial number: 60686
    [03-11 10:58:25-- TUYA Debug][mqc_app.c:951] Send MQTT Msg.P:4 N:60686 Q:1
    [03-11 10:58:25-- TUYA Err][tuya_ipc_stream_storage.c:1518] storage not inited
```
*   调用 TUYA_APP_Init_Stream_Storage 函数进行本地存储初始化，默认开启连续存储，如需开
启事件存储，注释 tuya_ipc_ss_set_write_mode(SS_WRITE_MODE_ALL)，开启 tuya_ipc_ss_ set_
write_mode(SS_WRITE_MODE_EVENT)，如下图所示：  
说明： 连续存储开启后，无需做其他操作，SDK 自动将录像保存至 SD 卡。设备启动后，本地存储初始化仅需要调用一次  

```C
OPERATE_RET TUYA_APP_Init_Stream_Storage(IN CONST CHAR_T *p_sd_base_path)
{
    STATIC BOOL_T s_stream_storage_inited = FALSE;
    if(s_stream_storage_inited == TRUE)
    {
        PR_DEBUG("The Stream Storage Is Already Inited");
        return OPRT_OK;
    }

    PR_DEBUG("Init Stream_Storage SD:%s", p_sd_base_path);
    OPERATE_RET ret = tuya_ipc_ss_init(p_sd_base_path, &s_media_info,MAX_EVENT_NUM_PER_DAY, NULL);
    if(ret != OPRT_OK)
    {
        PR_ERR("Init Main Video Stream_Storage Fail. %d", ret);
        return OPRT_COM_ERROR;
    }
    return OPRT_OK;
}

```
* 若已开启事件存储，当设备需要进行录像时，调用函数：tuya_ipc_ss_start_event
开启录像，调用：tuya_ipc_ss_stop_event 关闭录像，单个事件录像最长时间 10
分钟，超过 10 分钟若没有调用：tuya_ipc_ss_stop_event，则 SDK 会自动关闭录
像  
* 录像模式的切换：初始化录像设置后，在上报录像设置时调用tuya_ipc_ss_set_write_mode设置录像模式,再进行保存到本地后上报给云端,在收到切换录像模式的指令时再调用 tuya_ipc_ss_set_write_mode设置录像模式,再进行保存到本地后上报给云端   
* 对于sd卡的处理：本地存储仅支持 SD 卡使用 FAT32 格式，若检测到 SD 卡格式异常， 通过tuya_ipc_sd_get_status 函数中实现 SD 卡的检测，比如当检测到sd卡位exfat格式则返回SD_STATUS_ABNORMAL，当sd卡状态正常时则返回SD_STATUS_ABNORMAL，用户在 App 点击 SD 卡设置时，会有弹窗提醒SD 卡异常，引导客户对 SD 卡进行格式化，设备端实现相应的格式化接口IPC_APP_format_sd_card，对于sd卡的处理demo中大部分已经实现，开发时请参考demo根据实际需求进行更改  
* 注意:sd卡这一块的处理逻辑比较多,请仔细理解并根据实际需求修改  
* 至此本地录像功能开发完成  

* FAQ：
1、更新SDK后，增加对H265的支持后无法回放问题？  
答：确认tuya_ipc_p2p_demo.c中的__TUYA_APP_ss_pb_get_video_cb函数中是否有__TUYA_APP_media_frame_TO_trans_video这个转换函数，确认源码如下：  
```C
STATIC VOID __TUYA_APP_ss_pb_get_video_cb(IN UINT_T pb_idx, IN CONST MEDIA_FRAME_S *p_frame)
{
    TRANSFER_VIDEO_FRAME_S video_frame = {0};
    __TUYA_APP_media_frame_TO_trans_video(p_frame, &video_frame);
    tuya_ipc_playback_send_video_frame(pb_idx, &video_frame);
}
```
