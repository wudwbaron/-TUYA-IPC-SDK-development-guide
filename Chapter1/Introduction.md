# SDK简介  

*  如下图所示，获取到 SDK后，首先需要进行SDK服务注册，注册操作通过SDK配网模块建立，服务通道建立成功，客户可以将设备的音视频信息按照格式要求送入SDK开辟的缓存中，至此客户无需关心后续的操作。可通过APP端进行操控设备。基于原始的的音视频数据，SDK结合手机端APP可以实现设备的存储功能、点对点传输服务以及其他的第三方接入功能（例如：EchoShow和ChromeCast接入功能）。   

* 基本概念  

| 术语   | 描述 |
|------- | ---------|
| 配网	|	建立设备端、手机APP端、涂鸦云端之间的通信链路 |
| PID/Productkey | 产品ID，用来标示某一类产品，同一种类型的IPC设备共享同一个 产品ID。PID关联了产品具有的功能点 |
| UUID	|	Universally Unique Identifier,通用唯一识别码，由一台机器上生成的数字，保证所有机器都具有唯一性 |
| AUTHKEY	| 设备的授权值，在涂鸦云上已注册能够使用涂鸦云服务的服务码 |
| P2P ID	| 点对点服务ID, 3.0.0版本以上SDK不需要填写P2P ID，云端自动进行分配 |
| Token	| 二维码配网过程中，由云服务器生成的标识码，10min有效期 |
| DP点	| Date Point 设备与服务器数据交互的功能点 |
| Chromecast	| 谷歌电视棒或谷歌Home Hub 实时视频传输服务 |
| Echo Show	| 亚马逊音响实时视频传输服务|
| IFTTT	| 与其他品牌商建立设备联动服务，例：宝马 |
| OTA	|  设备在线升级功能	|  

* IPC_SDK开源代码路径
涂鸦提供开源的ipc_sdk代码，代码路径：https://github.com/TuyaInc/TUYA_IPC_SDK 。

* SDK流程图如下：  
<div align=center><img  src = "Introduction.assets/image-20191121105038812.png"alt="img" style="zoom:150%;"></div>   

* SDK工作流程  

首先，IPC启动后，IPC会首先读取本地的配置信息，识别和判断本地的状态，如果是第一次启用或者被重置，则进入WIFI配网流程；  
其次，配网过程中，通过声音或者LED提示用户，同时用户使用涂鸦智能APP，使用【 AP模式/二维码配网/Smartcfg配网 】将WIFI信息和用户信息告知IPC，IPC连接WIFI后进行注册，激活，登录TUYA MQTT服务器等操作；  
最后，上述整个流程完成后，IPC将最新的状态数据通过MQTT服务上报到云端和APP，同时开启P2P传输，Echoshow，云存储，ChromeCast等音视频服务。  
注：a、配网方式可能会根据IPC SDK和APP的升级发生变动。  
b、P2P传输，Echoshow，云存储，ChromeCast等音视频服务可能会根据IPC SDK的升级发生变动。  