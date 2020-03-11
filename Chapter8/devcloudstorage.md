# 设备端云存储实现
* 开发前说明：  
1、云存储事件包含移动侦测，但是和本地移动侦测的逻辑不能冲突，不能因为关闭了移动侦测，导致云存储无法触发，云存储的设计逻辑和移动侦测的开发逻辑无关即客户一旦买了云存储就必须在事件发生的时候会上传云存储录像到服务器，这个逻辑必须在云存储功能和移动侦测等其他事件开关功能开发完成后测试一遍，否则产品出到客户手里会引起不必要的投诉等售后问题（非常重要）  
2、涂鸦SDK默认已经实现软加密云存储，开发者不需要自己实现，如果设备资源够用，则可以考虑软件加密，如果设备本身带有加密芯片则推荐使用硬件加密  
* 软加密云存储实现
开发流程：  
1、调用TUYA_APP_Enable_CloudStorage初始化云存储  
```C
OPERATE_RET TUYA_APP_Enable_CloudStorage(VOID)
{
    AES_HW_CBC_FUNC aes_func;
    aes_func.init = AES_CBC_init;
    aes_func.encrypt = AES_CBC_encrypt;
    aes_func.destory = AES_CBC_destory;
    tuya_ipc_cloud_storage_init(&s_media_info, &aes_func);
    return OPRT_OK;
}
```
2、云存储只支持事件录像，不支持实时录像，当摄像机发生某事件时，必须调用 tuya_ipc_cloud_storage_event_add函数，SDK 才会将音视频信息上传至云端，当事件停止时调用：tuya_ipc_cloud_storage_event_delete 终止云存储事件录像，当tuya_ipc_cloud_storage_event_add 接口调用成功，返回event ID 用以调用 tuya_ipc_cloud_storage_event_delete 函数。若 tuya_ipc_cloud_storage 
_event_add 接口调用失败，返回：INVALID_EVENT_ID。云存储录像最终以列表的形式展现。  
当调用 tuya_ipc_cloud_storage_event_add 开启事件云存储，最长时间为 5 分 钟，5 分钟后若没有调用 tuya_ipc_cloud_storage_event_delete，SDK 将自动 结束事件云存储    
3、目前云储存的事件类型包括以下几种  
```C
typedef enum
{
    EVENT_TYPE_MOTION_DETECT,
    EVENT_TYPE_DOOR_BELL,       
    EVENT_TYPE_DEV_LINK,        /* event triggerred by other tuya cloud device */
    EVENT_TYPE_PASSBY,          /* for doorbell, detect someone passby */
    EVENT_TYPE_LINGER,          /* for doorbell, detect someone linger */
    EVENT_TYPE_LEAVE_MESSAGE,   /* for doorbell, a video message leaved */
    EVENT_TYPE_CALL_ACCEPT,     /* for doorbell, call is accepted */
    EVENT_TYPE_CALL_NOT_ACCEPT, /* for doorbell, call is not accepted */
    EVENT_TYPE_RECORD_ONLY,     /* only to start evnet record, no event reported or will be reported later */
    EVENT_TYPE_INVALID
}ClOUD_STORAGE_EVENT_TYPE_E;
```
* 硬件加密方式开发云存储(设备有加密芯片)  
* 开发流程：  
1、在项目加入以下头文件，文件名aes_inf.h  

```C
/*
aes_inf.h
Copyright(C),2018-2020, 涂鸦科技 www.tuya.comm
*/
#ifndef _AES_INF_H
#define _AES_INF_H

#include "tuya_cloud_types.h"
#include "tuya_cloud_error_code.h"

#ifdef __cplusplus
extern "C" {
#endif

typedef VOID (*AES128_ECB_ENC_BUF)(IN CONST BYTE_T *input, IN CONST UINT_T length, IN CONST BYTE_T *key,OUT BYTE_T *output);
typedef VOID (*AES128_ECB_DEC_BUF)(IN CONST BYTE_T *input, IN CONST UINT_T length, IN CONST BYTE_T *key,OUT BYTE_T *output);

typedef VOID (*AES128_CBC_ENC_BUF)(IN CONST BYTE_T *input, IN CONST UINT_T length, IN CONST BYTE_T *key, IN BYTE_T *iv, OUT BYTE_T *output);
typedef VOID (*AES128_CBC_DEC_BUF)(IN CONST BYTE_T *input, IN CONST UINT_T length, IN CONST BYTE_T *key, IN BYTE_T *iv, OUT BYTE_T *output);

typedef struct {
    AES128_ECB_ENC_BUF ecb_enc_128;
    AES128_ECB_DEC_BUF ecb_dec_128;
    AES128_CBC_ENC_BUF cbc_enc_128;
    AES128_CBC_DEC_BUF cbc_dec_128;
}AES_METHOD_REG_S;


typedef enum TUYA_HW_AES_MODE_ {
    TUYA_HW_AES_MODE_ENCRYPT,
    TUYA_HW_AES_MODE_DECRYPT,
} TUYA_HW_AES_MODE_E;


typedef enum TUYA_HW_AES_CRYPT_MODE_ {
    TUYA_HW_AES_CRYPT_MODE_ECB,
    TUYA_HW_AES_CRYPT_MODE_CBC,
    TUYA_HW_AES_CRYPT_MODE_CFB,
    TUYA_HW_AES_CRYPT_MODE_OFB,
} TUYA_HW_AES_CRYPT_MODE_E;


typedef struct TUYA_HW_AES_PARAM_ {
    TUYA_HW_AES_MODE_E        method;
    TUYA_HW_AES_CRYPT_MODE_E  encryptMode;
} TUYA_HW_AES_PARAM_S;


typedef struct TUYA_HW_AES_ {
    int (*aes_create)(void** pphdl, TUYA_HW_AES_PARAM_S* pparam);
    int (*aes_destroy)(void* phdl);
    int (*aes_setkey_enc)(void* phdl, const unsigned char *key, unsigned int keybits);
    int (*aes_setkey_dec)(void* phdl, const unsigned char *key, unsigned int keybits);
    int (*aes_crypt_ecb)(void* phdl, const unsigned char* input, size_t length, unsigned char* output);
    int (*aes_crypt_cbc)(void* phdl, const unsigned char* iv, unsigned int ivbits, const unsigned char *input, size_t length, unsigned char *output);
} TUYA_HW_AES_S;


OPERATE_RET aes_method_register(IN CONST AES_METHOD_REG_S *aes, IN CONST TUYA_HW_AES_S* pafunc);

VOID aes_method_unregister(VOID);

#ifdef __cplusplus
}
#endif
#endif
```  
2、根据加密芯片的不同实现以上头文件中的加解密函数  
3、调用aes_method_register注册硬件加密  
4、调用tuya_ipc_cloud_storage_init初始化云存储  
5、重复软加密的开发流程  