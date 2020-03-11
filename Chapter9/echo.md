# Echo show功能开发
* 开发流程：  
1、调用 Echow show 初始化函数：TUYA_APP_Enable_EchoShow_Chromecast  
2、设置需要拉取的视频流的通道es_param.vchannel = E_CHANNEL_VIDEO_SUB，建议子码流
3、确认选取的视频通道对应的音频格式，Echo show 音频要求：PCM 或 G711U
4、确认音视频 PTS 单位是否为微秒级
5、确认音视频的pts是否相差过大
	(1)初始pts的值是否一致，比如音视频的第一帧都是取0，或者第一帧都取自设备本地时间(建议使用)
* Echo show 出图优化建议：  
(1) 摄像机工作时，需要向 ringbuffer 中持续送入音视频流  
(2) 子码流码流控制在：300~512kbps,i帧间隔建议设置成1s一个  
(3) 当发现 Echo show 出图慢时，需要检查音频格式是否为：PCM 或 G711U  
(4)Echo show 功能定义：音视频流播默认放 5 分钟后会自动结束，可以联系项目经理在后台重新配置  
* 问题：
如果在调试echoshow功能的过程中设备重启了，可能的原因是没有忽略以下信号

![image-20200309164232313](echo.assets/image-20200309164232313.png)