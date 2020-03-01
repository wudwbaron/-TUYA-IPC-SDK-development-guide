# 设备上运行demo

* SDK，登陆GitHub：       
下载设备对应的编译链的SDK包
* 修改Demo代码  
修改user_main.c文件，将申请到的PID,UUID与AUTHKEY替换以下的宏定义  
说明： PID,UUID与AUTHKEY可以联系涂鸦项目经理获取或者在iot平台账号申请<div id = "pid" >(pid创建教程)</div>)  
```C
    #define IPC_APP_AUTHKEY         "tuyaOneAuthkeyForOneUUID"
    #define IPC_APP_AUTHKEY         "tuyaOneAuthkeyForOneUUID"
    #define IPC_APP_AUTHKEY         "tuyaOneAuthkeyForOneUUID"     
```
* 运行Demo代码  
* 编译生成可执行文件，命令如下：  
`$ ./build_app.sh demo_tuya_ipc`   
说明：可执行文件保存在编译后生成的output文件夹中  
* 设备是用有线连接网络：确认设备已经配置好网络，能够ping通a2.tuyacn.com(如果ping不通请检查设备网络连接状况和dns设置等)
* 设备是用无线连接网络：确认设备已经连接wifi并且能够ping通a2.tuyacn.com(如果ping不通请检查设备网络连接状况和dns设置等)，如果设备的wifi网卡名称为wlan0，则需要修改demo中tuya_ipc_wifi_demo.c里面WLAN_DEV的值为其他，不为wlan0即可(原因是sdk内部会操作WLAN_DEV，在demo运行阶段会破坏设备本身的网络连接状态)  
* 手机安装涂鸦智能App，主界面右上角加号，选择添加摄像机，安防传感品类选择智能摄像机，选择二维码配网输入ssid和psk后进行下一步生成二维码。将二维码通过微信“扫一扫”功能  
解析出配网信息，如下图所示：  
说明：AYm1YVV5jupJcF 为Token，有效期：10分钟  
{"s":"Tuya-Test","p":"88888888","t":"AYm1YVV5jupJcF"}  
进入编译生成的output文件夹，找到tuya_ipc_demo_tuya_ipc文件所在目录并执行以下命令：  
`$ ./ tuya_ipc_demo_tuya_ipc -m 2 -r “yyyy” -t “xxxx”`  
说明：执行以下命令时，需要在 “xxxx”中填入完整Token,"yyyy"中填入音视频所在的绝对路径，例如视频的路径为%s/resource/media/demo_video.264，其中%s表示”yyyy“中的内容(如果能够正常添加设备，但是app预览界面没有视频和声音，请排查下-r的音视频路径）
* App显示二维码界面点击“下一步”，等待直至配网功能。Demo配网成功操作页面如下图  
所示：  

 <div align=center><img  src = "demo.assets/wps5.png"alt="img" style="zoom:150%;"></div>  
通过以上步骤可以简单的上手涂鸦IPC提供的sdk，在实际应用中，客户需要将自己实际的音视频数    据流放入SDK开辟的缓存中，即可在APP端浏览到自己的IPC画面。  

* demo案例执行流程  
 <div align=center><img  src = "demo.assets/wps6.jpg"alt="img" style="zoom:150%;"></div> 



