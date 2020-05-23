# 本地存储预录功能开发  

* 功能说明：在触发移动侦测等事件的时候，预录事件发生前一段时间的音视频  
#  常供电设备功能实现：  
* 1、本地存储和ringbuff初始化以后调用tuya_ipc_ss_set_pre_record_time这个接口  

```C
/**
 * \fn VOID tuya_ipc_ss_set_pre_record_time(UINT_T pre_record_second);
 * \brief set pre-record time, invoke this API only if needed, or it is 2 seconds by default.
 * \Should be invoked once after init and before start.
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_ss_set_pre_record_time(UINT_T pre_record_second);

```
```C
/* initialize one ring buffer for one stream(one channel)
channel: ring buffer channel num, multipul channel for one 
bitrate: bitrate in Kbps
fps: framerate pre second
max_buffer_seconds: should be more than 1 GOP and less than 10 sencond. Set to 0 as default(10s).
requestIframeCB: call back function to request one I frame from video decoder. set to NULL if not needed or for NON-video stream.
*/
OPERATE_RET tuya_ipc_ring_buffer_init(CHANNEL_E channel, UINT_T bitrate, UINT_T fps, UINT_T max_buffer_seconds, FUNC_REQUEST_I_FRAME requestIframeCB);
```

* 注意：pre_record_second设置的值不能大于 (max_buffer_seconds-2）  
# 低功耗设备休眠唤醒时本地存储录像的预录功能开发  
* 功能说明： 低功耗设备唤醒到设备正常同步时间再到正常录像会导致最开始塞入SDK的音视频流的时间戳是异常的，所以需要特殊处理(本功能的实现需要设备具备本地rtc)  
* 功能开发说明：  
* 1、设备休眠前保存时间和设备当前时区到设备  
* 2、在tuya_ipc_start_sdk前把rtc时间和时区设置到SDK  
	* 设置SDK时间：  
	```C
    /**
    * \fn OPERATE_RET tuya_ipc_set_service_time(IN TIME_T new_time_utc)
    * \brief set time of tuya SDK
    * \return OPERATE_RET
    */
    OPERATE_RET tuya_ipc_set_service_time(IN TIME_T new_time_utc);
	```
	* 设置时区: OPERATE_RET uni_set_time_zone(IN CONST CHAR_T *time_zone)，这个接口没有头文件，用extern申明，时区格式是+08:00  
* 3、本地存储和ringbuf初始化以后调用tuya_ipc_ss_set_pre_record_time这个接口  

```C
    /**
     * \fn VOID tuya_ipc_ss_set_pre_record_time(UINT_T pre_record_second);
     * \brief set pre-record time, invoke this API only if needed, or it is 2 seconds by default.
     * \Should be invoked once after init and before start.
     * \return OPERATE_RET
     */
    OPERATE_RET tuya_ipc_ss_set_pre_record_time(UINT_T pre_record_second);

```
```C
    /* initialize one ring buffer for one stream(one channel)
    channel: ring buffer channel num, multipul channel for one 
    bitrate: bitrate in Kbps
    fps: framerate pre second
    max_buffer_seconds: should be more than 1 GOP and less than 10 sencond. Set to 0 as default(10s).
    requestIframeCB: call back function to request one I frame from video decoder. set to NULL if not needed or for NON-video stream.
    */
    OPERATE_RET tuya_ipc_ring_buffer_init(CHANNEL_E channel, UINT_T bitrate, UINT_T fps, UINT_T max_buffer_seconds, FUNC_REQUEST_I_FRAME requestIframeCB);
```
* 注意：pre_record_second设置的值不能大于 (max_buffer_seconds-2）

* 4、设备上线后同步服务器时间到本地