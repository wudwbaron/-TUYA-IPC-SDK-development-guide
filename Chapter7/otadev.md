# OTA 开发

* OTA操作流程说明  
1、设备上的版本号是用IPC_APP_VERSION表示，版本形式是xx.xx.xx即最大版本号为99.99.99，设备上电后SDK就会把IPC_APP_VERSION上报给云端，开发者不用做特别上报版本号的操作  
2、设备上收到升级请求之前需要准备的东西：  
(1)、iot平台上传固件[(操作说明)](https://docs.tuya.com/zh/iot/configure-in-platform/advanced-features/firmware-upgrade-operation-guide)  
(2)、当上传的固件版本号比当前设备的固件版本号大，APP上会有升级的提示，点击app升级按钮  
(3)、设备收到升级请求后进行升级处理  
* 设备端固件升级开发流程：  
* 当app端点升级后：  
  sdk会调用IPC_APP_Upgrade_Inform_cb，参数包含：固件下载的 url（下载链接）、md5（用来做文件校验）以及 size（文件的大小）    
在调用前tuya_ipc_upgrade_sdk前请做以下动作释放内存，如果内存完全够用可以不做以下处理：  
释放本地存储 tuya_ipc_ss_set_write_mode，关闭本地录像功能，然后调用tuya_ipc_ss_uninit()、释放p2p内存tuya_ipc_tranfser_close，释放云存储：tuya_ipc_cloud_storage_uninit  
定义一个文件路径用来保存下载的ota固件，文件描述符p_upgrade_fd指向保存的路径  
```C  
VOID IPC_APP_Upgrade_Inform_cb(IN CONST FW_UG_S *fw)
{
    PR_DEBUG("Rev Upgrade Info");
    PR_DEBUG("fw->fw_url:%s", fw->fw_url);
    PR_DEBUG("fw->fw_md5:%s", fw->fw_md5);
    PR_DEBUG("fw->sw_ver:%s", fw->sw_ver);
    PR_DEBUG("fw->file_size:%u", fw->file_size);
    FILE *p_upgrade_fd = fopen(s_mgr_info.upgrade_file_path, "w+b");
    tuya_ipc_upgrade_sdk(fw, __IPC_APP_get_file_data_cb, __IPC_APP_upgrade_notify_cb, p_upgrade_fd);
}
```
* 接下来tuya_ipc_upgrade_sdk会出处理OTA功能  
```C
 * \brief OTA, upgrade via TUYA SDK
 * \param[in] fw firmware: infomation
 * \param[in] get_file_cb: callback function during downloading fw
 * \param[in] upgrd_nofity_cb: callback function when downloading fw fininsh
 * \param[in] pri_data: data transferred between callback functions
 * \return OPERATE_RET
 */
OPERATE_RET tuya_ipc_upgrade_sdk(IN CONST FW_UG_S *fw,IN CONST GET_FILE_DATA_CB get_file_cb,IN CONST UPGRADE_NOTIFY_CB upgrd_nofity_cb,IN PVOID_T pri_data);  
```
get_file_cb表示下载片段的ota文件到本地文件，其中tuya_ipc_upgrade_progress_report 函数，上报 OTA 下载的进度，上报的进度值建议小于 99%  
```C
OPERATE_RET __IPC_APP_get_file_data_cb(IN CONST FW_UG_S *fw, IN CONST UINT_T total_len,IN CONST UINT_T offset,
                             IN CONST BYTE_T *data,IN CONST UINT_T len,OUT UINT_T *remain_len, IN PVOID_T pri_data)
{
    PR_DEBUG("Rev File Data");
    PR_DEBUG("total_len:%d  fw_url:%s", total_len, fw->fw_url);
    PR_DEBUG("Offset:%d Len:%d", offset, len);
    int percent = ((offset * 100) / (total_len+1));
    tuya_ipc_upgrade_progress_report(percent);
    FILE *p_upgrade_fd = (FILE *)pri_data;
    fwrite(data, 1, len, p_upgrade_fd);
    *remain_len = 0;
    return OPRT_OK;
}
```
当ota文件下载完成后,sdk会调用以下回调函数，请在下面的函数中实现：  
(1)、对下载到本地的固件进行 md5 计算，与服务器下发的md5值进行对比，结果相同则下载固件无误可以去做升级处理ota固件替换和升级处理  
(2)、固件替换前，需要备份 db 文件，固件替换成功后，对比 db 文件替换固件前后的 md5 值，若相同，则可以直接重启，若不相同，则将备份的 db 文件替换现有的 db 文件再重启设备  
(3)、替换固件后，重启设备  
(4)、固件升级时，手机 App 进度显示会暂停在 92%~98%之间，此时，固件已完成下载，App 正在
等待设备重启并上报新固件版本号，版本号上报成功后，App 将显示“升级成功“。   
(5)、App 等待设备替换固件与上报新版本号的时间为 1 分钟，若设备流程超过此时间，需要告知
项目经理 PID 后台进行配置  
```C
VOID __IPC_APP_upgrade_notify_cb(IN CONST FW_UG_S *fw, IN CONST INT_T download_result, IN PVOID_T pri_data)
{
    FILE *p_upgrade_fd = (FILE *)pri_data;
    fclose(p_upgrade_fd);

    PR_DEBUG("Upgrade Finish");
    PR_DEBUG("download_result:%d fw_url:%s", download_result, fw->fw_url);

    if(download_result == 0)
    {
        /* The developer needs to implement the operation of OTA upgrade, 
        when the OTA file has been downloaded successfully to the specified path. [ p_mgr_info->upgrade_file_path ]*/
    }
    //TODO
    //reboot system
}
```
