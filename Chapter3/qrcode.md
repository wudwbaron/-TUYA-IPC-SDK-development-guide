# 二维码配网开发

具体流程如下：  

<div align=center><img  src = "qrcode.png"alt="img" style="zoom:150%;"></div>  
开发流程：  
* IPC_APP_Init_SDK 函数中，选择：WIFI_INIT_AUTO 配网模式,token 参数填 NULL（很重要）  

* 确认代码中WLAN_DEV和当前设备使用的wifi名称一致（默认wlan0），SDK的demo代码中已经实现了一些对wifi的操作(设置wifi模块模式切换通道等)，如果日志中有某个指令的运行错误请检查当前系统是否包含该命令  

* 确认在hwl_wf_sniffer_set中是否开启了thread_qrcode线程，SDK中demo代码默认是没有开启这个线程(很重要)  

* 在__tuya_linux_get_snap_qrcode中实现识别app上二维码的动作,并返回识别的信息给tuya_ipc_direct_connect这个函数（很重要）   
* <a href="#zbar库的使用"> zbar库的使用  
* 如果SDK解析到了正确的配网信息接下来SDK会调用函数：hwl_wf_wk_mode_set，将 WiFi 状态设置为 station 模式  

* 接下来SDK会主动调用hwl_wf_station_connect这个接口，客户需要自己在这个接口实现wifi连接路由器的动作（很重要）  

* 连接路由器后，返回设备的真实状态给SDK，hwl_wf_get_ip，hwl_wf_get_mac以及hwl_wf_station_stat_get(很重要),设备所有状态为如下结构体  
```
    typedef enum {
        WSS_IDLE = 0,                       // not connected
        WSS_CONNECTING,                     // connecting wifi
        WSS_PASSWD_WRONG,                   // passwd not match
        WSS_NO_AP_FOUND,                    // ap is not found
        WSS_CONN_FAIL,                      // connect fail
        WSS_CONN_SUCCESS,                   // connect wifi success
        WSS_GOT_IP,                         // get ip success
    }WF_STATION_STAT_E;
```
* 当用户输入了错误的wifi密码的情况下：应该将设备的状态置为WSS_PASSWD_WRONG，并且通过语音或者提示灯提示用户重置设备后重新配网。  
* 注意：请先理解上图的配网流程再进行开发  
* [FAQ](https://wudwbaron.github.io/FAQ/connectwifi.html)  

# zbar库的使用

* 概述  
zbar 是条形码/二维码扫描工具，支持摄像头及图片扫描，支持多平台，同时提
供了二维码扫描的 API 接口。  
下载链接：https://ayera.dl.sourceforge.net/project/zbar/zbar/0.10/zbar-0.10.tar.bz2  
参考代码：  

  ```C
   int qrcode_parse_from_buffer(void *y8data, int w, int h, char* pqrcode)
    {
    int ret = -1;
    zbar_image_scanner_t *scanner = NULL;
    if(y8data == NULL || pqrcode == NULL) 
    {
    return -1;
    }
    scanner = zbar_image_scanner_create();
    uint32_t srcfmt = fourcc('Y','8','0','0');
    zbar_image_t *image = zbar_image_create();
    zbar_image_set_userdata(image, NULL);
    zbar_image_set_format(image, srcfmt);
    zbar_image_set_size(image, w, h);
    zbar_image_set_data(image, y8data, w * h, zbar_image_get_userdata);
    int n = zbar_scan_image(scanner, image);
    const zbar_symbol_t *symbol = zbar_image_first_symbol(image);
    if (NULL == symbol) 
    {
    goto exit;
    }
    for(; symbol; symbol = zbar_symbol_next(symbol)) 
    {
     zbar_symbol_type_t typ = zbar_symbol_get_type(symbol);
     const char *data = zbar_symbol_get_data(symbol);
     if(data != NULL) 
     {
     strncpy(pqrcode, data, zbar_symbol_get_data_length(symbol));
     ret = 0;
     printf("decoded %s symbol \"%s\"\n", zbar_get_symbol_name(typ), 
    data);
     } }
    exit:
    zbar_image_destroy(image);
    zbar_image_scanner_destroy(scanner);
    return ret;
    }  
  ```
* 建议: 扫描时，按照 App 提示，手机与摄像机保持在合适的距离范围扫码时，图像分辨率不需要太大，320*240 以上即可，在扫码过程中尽量关闭其他不必要的操作，节约设备资源  

* 编译脚本  

* 构建一个 build.sh 编译脚本，内如如下：
```C  
    #!/bin/bash
    CURDIR=`pwd`
    FILENAME=zbar-0.10.tar.gz
    DIRNAME=zbar-0.10
    DOWNLOADURL=http://101.96.10.46/downloads.sourceforge.net/project/zbar/z
    bar/0.10/zbar-0.10.tar.bz2
    if [ ! -f ${FILENAME} ]; then
    wget ${DOWNLOADURL}
    fi
    tar -xzvf ${FILENAME}
    mkdir out
    
    #build lib
    cd ${DIRNAME}
    export CFLAGS=""
    ./configure --disable-video --without-jpeg --without-imagemagick
    --without-gtk --without-python
    --without-qt --prefix=${CURDIR}/out
    make
    make install
    cd ..  
```
* 编译结束后，在 out 目录会生成对应的库及使用到的头文件，拷贝到需要使用的工程即可。  