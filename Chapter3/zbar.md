# zbar库的使用

* 概述  
zbar 是条形码/二维码扫描工具，支持摄像头及图片扫描，支持多平台，同时提
供了二维码扫描的 API 接口。  
下载链接：https://ayera.dl.sourceforge.net/project/zbar/zbar/0.10/zbar-0.10.tar.bz2  
参考代码：  

  ```
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
```  
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