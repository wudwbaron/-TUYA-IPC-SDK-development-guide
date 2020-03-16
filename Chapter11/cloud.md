# 云存储问题

1、云存储开发流程？  
成功购买服务后，调用云存储初始化函数：TUYA_APP_Enable_CloudStorage  
SDK 默认对数据采用软件加密的方式，加密接口：OpensslAES_CBC128_encrypt 使用 AES 
CBC 方式。若软件加密给 MCU 带来较大的负载，可改用硬件加密方式   
调用 tuya_ipc_cloud_storage_event_add函数，SDK 才会将音视频信息上传至云端，调用tuya_ipc_cloud_storage_event_delete 终止云存储事件录像  

2、云存储服务怎么开通？  
客户自助开通：在IOT平台-服务市场-摄像机服务-视频云存储中开通服务即可         

3、开发过程中如何测试云存储？   
在app云储存入口购买试用套餐,每个 UUID 都有一次体验 0.01 元购买云存储服务的权限  

4、云存储无法预览，设备端提示cloud storage type error 2？  
确认是否开通了云储存服务，如果自己不能确认，可以找项目经理进行确认  

5、云储存事件录像和本地事件录像有关系吗？  
没有关系，在开发过程中应注意云储存的逻辑和本地录像逻辑分离，即本地事件录像的开关对云存储事件录像不影响  

6、云存储视频出现跳秒现象要如何定位问题？  
在播放云存储时，检查输出的日志：  
日志若有打印：use r 1 jump to newest I frame，则：网络带宽不足，超过了buffer的时间长度  
日志若有打印：memory insufficient to buffer data，则：输出的码流超出限定值1.5Mbps  

7、云存储视频出现回放无视频问题如何定位？  
invalid input snapshot_size 119108 type 0，若有此打印，说明：云存储上传到图片太大，超过100KB，需要客户对图片做对应的处理，sdk认为图像异常，会阻止视频的上传，上传的图片无分辨率的限制

