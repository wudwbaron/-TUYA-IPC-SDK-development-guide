#  概述  
* 涂鸦SDK搭建了一套完整的用以引导设备配网的函数框架，此章节主要阐述配网的流程以及各框架函数的用途。建议开发者按照文档介绍，在对应的框架函数下，实现各功能点的具体操作。SDKdemo中已实现框架函数的调用逻辑，开发者不需要再做对应开发。  
* 开发者配网方式选择  
 目前SDK可支持二维码配网，EZ配网，AP配网，有线配网。  
 WIFI_INIT_AUTO模式初始化SDK，此时sdk同时支持维码配网，EZ配网，有线配网，此时，app上 选择 维码配网，EZ配网，有线配网都能正常配网。  
 WIFI_INIT_AP模式初始化SDK，此时sdk同时支持二维码配网，AP配网，此时，app上选择二维码配网和AP配网都能正常配网。  
 如上EZ配网和AP配网不能同时共存，需要用户手动去切换，使上层应用以不同的模式初始化SDK。  
*  基本参数填写分别修改DB文件、OTA文件、本地录像文件的保存路径，宏定义如下：  
  说明：  
 DB文件中保存设备的配网信息，需要保存至掉电不丢失的路径下  
 DB文件与本地录像文件保存路径需要检查结尾是否有“/”  
 ```C
     #define IPC_APP_STORAGE_PATH    "/tmp/"   
     #define IPC_APP_UPGRADE_FILE    "/tmp/upgrade.file"  
     #define IPC_APP_SD_BASE_PATH    "/tmp/"   
 ```
* 修改PID、UUID、AUTHKEY的宏定义：  
 说明：PID需要联系项目经理获取  
 UUID、AUTHKEY需要联系商务获取  
 ```C
     #define IPC_APP_PID             "gpkguYNp7yI4k413"    
     #define IPC_APP_UUID            "tuyaOneUuidForOneDevice"   
     #define IPC_APP_AUTHKEY         "tuyaOneAuthkeyForOneUUID" 
 ```
* 修改版本号的宏定义：  
 说明：版本号必现按照格式：xx.xx.xx进行填写  
 ```C
    #define IPC_APP_VERSION         "1.2.3"  
 ```
 至此，SDK基本参数已完成填写，进入配网模式。        
 正式使用SDK的时候将main函数中IPC_APP_Init_SDK之前的代码注释掉，不需要从应用外面获取参数，IPC_APP_Init_SDK的参数mode表示配网模式，本篇上面的概述中有说明，token填NULL即可，token会在配网的过程中从app中拿到。
