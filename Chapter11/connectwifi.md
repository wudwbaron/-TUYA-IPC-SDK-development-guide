# 配网问题   
1、SDK支持哪些配网方式？   
SDK支持二维码配网、EZ配网、AP配网、有线配网。  

2、SDK支持哪些配网方式可以同时共存？   
（1）二维码配网、EZ配网、有线配网  
（2）二维码配网、AP配网  

3、如果需要产品需要同时支持EZ配网和AP配网有什么解决方案？  
给每一种配网方式设定一种声音提示或者亮灯提示，配网方式由硬件切换；比如当前处于ez配网长按reset按键后设备重置，重启后变成ap配网。   

4、开发配网前要准备什么？  
（1）SDK开发手册
（2）与芯片匹配编译链的SDK源码
（3）PID、UUID、AUTHKEY（可以找项目经理或者FAE提供）   

5、配网失败日志打印为：mqtt task is not permit to execute. short sleep..   
没有在函数hwl_wf_station_stat_get 中告知SDK已获取到路由器IP，解决方法：       
在hwl_wf_station_stat_get中返回WSS_GOT_IP给SDK     

6、有线配网无法找到设备?   
（1）查看hwl_wf_get_ip函数中返回的ip是否与当前手机在同一局域网                
（2）hwl_wf_station_stat_get中是否返回WSS_GOT_IP   

7、配网过程中打印Product Does not exist、http_gw_active service verify fail, unactive and restart  
pid填写错误，需要找项目经理或者FAE核对PID信息  

8、配网成功重启设备后又进入了配网模式？    
设备端将db配置文件保存到了临时目录，导致设备重新上电后，设备无法找到tuya_user.db文件，重新进入了配网模式，需要将db文件保存至掉电可保存的路径下。    

9、设备运行时串口一直打印hwl_wf_wk_mode_get:WIFIGetMode这个是什么意思？  
hwl_wf_wk_mode_get是获取wifi的工作模式，共有lowpower mode、sniffer mode、station mode、ap mode、station+ap mode5中模式   

10、纯有线配网需要怎么开发？  
需要向FAE重新打包纯有线配网的SDK，采用WIFI_INIT_NULL初始化SDK，在hwl_bnw_get_ip中返回设备当前网络ip即可。  

11、WIFI_INIT_AUTO、WIFI_INIT_AP、WIFI_INIT_DEBUG、WIFI_INIT_NULL四种初始化SDK的方式代表什么配网方式？   
WIFI_INIT_AUTO：SDK可以同时支持二维码配网、EZ配网、有线配网。  
WIFI_INIT_AP：SDK可以同时支持二维码配网、AP配网  
WIFI_INIT_DEBUG：SDK以debug模式初始，关于debug模式见开发手册第二章demo运行  
WIFI_INIT_NULL：仅在纯有线配网的SDK中使用,(注意：纯有线的SDK github上没有提供下载链接，如有需要找FAE提供)

12、各种配网方式流程图：    
EZ配网  
<div align=center><img  src = "connectwifi.assets/ez.png"alt="img" style="zoom:150%;"></div>   
二维码配网  
<div align=center><img  src = "connectwifi.assets/qrcode.png"alt="img" style="zoom:150%;"></div>   
AP配网  
<div align=center><img  src = "connectwifi.assets/ap.png"alt="img" style="zoom:150%;"></div>   
有线配网  
<div align=center><img  src = "connectwifi.assets/wired.png"alt="img" style="zoom:150%;"></div>   



