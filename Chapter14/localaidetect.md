# 本地ai功能开发

* 本地 AI 功能旨在为已具备 AI 识别算法的设备提供 AI 数据传输，标记，同步等
与 APP 交互的功能，与云端ai相比，本地进行识别准确率要高很多  
* 当使用的场景是当设备检测人脸或者人形的时候只给app推送一个告警，则不需要进行下面流程，只需要走自定义事件告警，参考自定义事件图文告警开发  
* 若需要对人脸进行标记功能，请走以下流程  
* 开发流程：   
1、 联系项目经理，提供 PID，开通本地 AI 检测功能  
2、调用函数：tuya_ipc_ai_face_detect_storage_init 初始化本地 AI 功能  
3、设备每次上电，调用函数：tuya_ipc_ai_face_all_get 与云端同步已成功标记人脸的数据  
说明：相同 PID 的产品在同一个 App 家庭中，调用 tuya_ipc_ai_face_all_get 接口，可以同步获取到其他设备所被标记的人脸数据，建议设备定时调用tuya_ipc_ai_face_all_get 接口，同步云端人脸标记数据  
4、调用函数：tuya_ipc_ai_face_pic_download 下载云端同步获取到的图片信息    
说明：函数 tuya_ipc_ai_face_all_get 获取的 URL 下载前，可以与上一次获取到的 URL
做对比，只下载新 URL  
5、 设备端进行本地人形识别功能，当成功检测到新人脸时，调用函数tuya_ipc_ai_face_new_report 实现新人形数据上报，AI_NEW_FACE_IMAGE_CTL_S 结构体需要传入人形识别评定分数   
7、 APP 成功标记或删除标记后，设备端回调函数：tycam_ai_face_cfg_func 被调用，用以更新本
地人形识别的特征库：  
8、若设备端识别到已标记的人脸，调用函数：tuya_ipc_ai_face_signed_report 实现已标记人脸的
数据上报  
9、本地 AI 检测功能没有固定占用内存空间，不需要做功能反初始化  