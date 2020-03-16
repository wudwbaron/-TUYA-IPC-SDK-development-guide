# 云端ai功能开发
* 开发流程：  
1、调用函数：tuya_ipc_ai_detect_storage_init 进行功能初始化  
```C
extern IPC_MEDIA_INFO_S s_media_info;
OPERATE_RET TUYA_APP_Enable_AI_Detect()
{
    tuya_ipc_ai_detect_storage_init(&s_media_info);

    return OPRT_OK;
}
```
2、 当发现画面发生变化时，调用函数：tuya_ipc_ai_detect_storage_start 进行人形检测或者人脸识别，当函数返回成功，则存在 AI 服务，若函数返回不为 0，则无 AI 检测服务，需要开启普通移动侦测检测功能  
```C
/**
 * \fn OPERATE_RET tuya_ipc_ai_detect_storage_start  
 * \brief 
 * \      When the motion started, use this api to start ai detect, 
 * \      to check is there any body or face in the pic. 
 * \      Don't need to care about the pic to check, this api well get 
 * \      the pic by VOID tuya_ipc_get_snapshot_cb(char* pjbuf, int* size),
 * \      so you need to complete the tuya_ipc_get_snapshot_cb function
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_ai_detect_storage_start();
```
3、开启人形，人脸检测后SDK会去调用tuya_ipc_get_snapshot_cb将图片上传至云端，若 AI 人形检测成功或者人脸检测成功，云端会自动向 App 推送检测结果(图片)  
```C
VOID tuya_ipc_get_snapshot_cb(char* pjbuf, unsigned int* size)
{
    get_motion_snapshot(pjbuf,size);
}
```
4、当发现画面停止变化时，调用函数 tuya_ipc_ai_detect_storage_stop，停止 AI 人形检测  
```C
/**
 * \fn OPERATE_RET tuya_ipc_ai_detect_storage_stop  
 * \brief When the motion stopped, use this api to stop detect
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_ai_detect_storage_stop();
```
5、在 OTA 需要优化内存时，可以调用函数：tuya_ipc_ai_detect_storage_exit 退出 AI 人形检测功

能，优化内存使用空间  
```C
/**
 * \fn OPERATE_RET tuya_ipc_ai_detect_storage_exit  
 * \brief Exit ai detect function.
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_ai_detect_storage_exit();

```  
6、云端人脸识别和人形检测，设备上都可以用同一套流程，开通与否取决于iot平台以及后台配置  

