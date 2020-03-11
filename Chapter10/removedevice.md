# app端移除设备开发
* 开发流程：  
1、当客户在app点击移除设备后，云端会将这个设备和当前app账号解除绑定关系，app会把这个设备从设备列表移除，如果这个时候设备是在线的则会调用IPC_APP_Reset_System_CB 来重置设备   
2、在该函数中实现设备的重启与对 DP 点数据文件进行重置操作，不需要 删除 DB 文件，SDK会自行删除tuya_enckey.db  tuya_user.db  tuya_user.db_bak这三个涂鸦SDK自己生成的db文件，设备重启后会重新创建(如果设备重启后无法进入配网状态，请在IPC_APP _Reset_System_CB函数加上实现删除db文件的操作)    
```C
VOID IPC_APP_Reset_System_CB(GW_RESET_TYPE_E type)
{
    printf("reset ipc success. please restart the ipc %d\n", type);
    IPC_APP_Notify_LED_Sound_Status_CB(IPC_RESET_SUCCESS);
    //TODO
    /* Developers need to restart IPC operations */
}
```
3、当设备没在线时，因为绑定关系没有了，所以无法被使用，需要重置设备重新进入配网状态，具体实现在下一节  
