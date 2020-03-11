# IoT平台视频云存储自动化开通

* 功能开通流程  

1.进入IoT平台服务市场，选择“摄像机服务”，找到“视频云存储”，点击“开通服务”。  

<div align=center><img  src = "iotcloud.assets/wps1.jpg"alt="img" style="zoom:100%;"></div>

2.在新页面中点击“提交申请”，云存储分成协议会自动开通并生效  
<div align=center><img  src = "iotcloud.assets/wps2.jpg"alt="img" style="zoom:100%;"></div>

3.分成协议生效后（或之前已在线申请成功），点击“我的服务”，找到视频云存储的管理入口，点击打开新页面  
<div align=center><img  src = "iotcloud.assets/wps3.jpg"alt="img" style="zoom:100%;"></div>

4.在新页面中打开“开通产品”一栏，点击“新增产品”  
<div align=center><img  src = "iotcloud.assets/wps4.jpg"alt="img" style="zoom:100%;"></div>

5.在此步骤中，需要客户手中有一台需要开通云存储功能的PID的样品，并且确保：  

-设备所属的PID在当前IoT账号下  

-设备已经配网成功，在App上可以看到，并且设备是在线状态  

-该PID未开通云存储服务  

然后将该样品的设备id填写到页面中，点击“下一步”  
<div align=center><img  src = "iotcloud.assets/wps5.jpg"alt="img" style="zoom:100%;"></div>

6.在整个自动化流程开始之前，系统会对该设备的设备id进行一系列校验，常见的错误包括以下几种：  
  <div align=center><img  src = "iotcloud.assets/wps6.jpg"alt="img" style="zoom:100%;"></div>  
  <div align=center><img  src = "iotcloud.assets/wps7.jpg"alt="img" style="zoom:100%;"></div>  
  <div align=center><img  src = "iotcloud.assets/wps8.jpg"alt="img" style="zoom:100%;"></div>  

7.流程开始后，云端会模拟给当前设备开通上传视频的权限（此时App上仍然没有云存储功能入口，整个过程在后端静默执行），并校验上传文件的正确性；过程中需要在设备面前进行一定的运动，保证设备有捕捉到事件从而可以正常发起视频的上传过程。整个验证过程大概持续2-3分钟，在此过程中请不要关闭当前页面。  

  <div align=center><img  src = "iotcloud.assets/wps9.jpg"alt="img" style="zoom:100%;"></div>  

8.当云端验证设备具备正常上传云存储视频的能力后，页面提示服务开通成功。此时App上会出现云存储功能的入口（可能需要清除App缓存），该PID的云存储功能就可以正常使用了。  

<div align=center><img  src = "iotcloud.assets/wps10.jpg"alt="img" style="zoom:100%;"></div>

9.申请过云存储功能的设备id，都会记录在“开通产品”一栏的列表内，并且标明申请时设备的固件版本，建议工厂出货不低于该版本。  

<div align=center><img  src = "iotcloud.assets/wps11.jpg"alt="img" style="zoom:100%;"></div>

注意：已经出货的产品，在进行此操作之前，需要确保最新的固件已发布升级，否则有可能出现样品固件版本支持云存储但实际出货产品固件比较老无法支持云存储的问题。  

10、在调试阶段每个 UUID 都有一次体验 0.01 元购买云存储服务的权限，如果app上显示云储存过期请联系项目经理  

FAQ：  

Q：以前已经开通过云存储功能的PID会显示在哪里？  

A：在“服务商品”下可以看到有效商品列表和PID的对应关系。目前这个页面的信息结构不是很友好，会做进一步迭代。  

Q：IoT平台只有中国区，那么自动化开通过程需要的样品设备的设备id是不是也必须是中国区设备id？  

A：不是。虽然IoT平台只有中国区，但是整个自动化开通服务的业务在全球各个区域都有部署，而且为了方便客户，这个过程也不需要客户选择设备id所在区域，系统会自动遍历各个区域，只要设备id在某个区域是有效的就可以。  

Q：如果客户需要用预存款形式定制服务内容，是否可以用这种方式？  

A：预存款形式，客户自己收费并定制服务内容，暂时不支持在IoT平台自助配置。如果客户有相关需求，目前仍然需要单独联系BD处理。  

 