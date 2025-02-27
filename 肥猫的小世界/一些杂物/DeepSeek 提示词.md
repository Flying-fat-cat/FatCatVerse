这是一个嵌入式项目，目的是做一个智能药盒，现在正在进行部分功能的开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，并通过VS CODE中keil插件直接调用UV4.EXE实现编译和烧录，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。
3.将接收到的MQTT消息中的"code_name"拆分出来，并保存
4.USART3连接一块大彩的串口屏幕，用于将获取到的CODE_NAME转化成药品名字显示，目前仅有code_name，并没有转成药品名称。

下一步要实现的功能：
1.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。

现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向往询问。同时请你尝试优化完善项目，实现下一个功能，如果还有什么需要我提供的信息，请向我提问，我会告知你。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。


补充以下信息：
1.在ESP8266_wifi.c中是否已存在wifi_scan_ap函数？如果有请提供其实现： 在ESP8266_wifi.c中，我通过uint8_t AT_Send_Check(uint8_t *buff, uint8_t *readData,uint8_t *checkBuff, uint8_t check, uint16_t outTime)函数实现AT指令的发送和接收指令时的检查，其中的参数含义为：//发送的数据、用来接收的地址、需要检测的命令、是否检查、超时时间 检测成功返回1 失败返回0。代码中有调用该函数的例子，比如：case 2://AT测试连接正常 if(AT_Send_Check((uint8_t *)"AT",esp.readData,(uint8_t *)"OK",1,1000)) { if(AT_num++ > 5) { connect_step = 3; USART1_SendString("AT测试连接正常\r\n"); } } 2.当前使用的AT指令固件版本（例如是否使用AT+CWLAP的扩展响应格式）： 我不太理解你的意思，但可以向你提供该部分的数据手册内容和例子:内容：AT+CWLAP：扫描当前可用的 AP 设置命令 功能： 列出符合特定条件的 AP，如指定 SSID、MAC 地址或信道号 命令： AT+CWLAP=[<ssid>,<mac>,<channel>,<scan_type>,<scan_time_min>,<scan_time_max>] 执行命令 功能： 列出当前可用的 AP 命令： AT+CWLAP 响应： +CWLAP:(<ecn>,<ssid>,<rssi>,<mac>,<channel>,<freq_offset>,<freqcal_val>,<pairwise_cipher>,<group_cipher>,<bgn>,<wps>) OK 参数 <ecn>：加密方式 0: OPEN 1: WEP 2: WPA_PSK 3: WPA2_PSK 4: WPA_WPA2_PSK 5: WPA2_ENTERPRISE 6: WPA3_PSK 7: WPA2_WPA3_PSK 8: WAPI_PSK 9: OWE <ssid>：字符串参数，AP 的 SSID <rssi>：信号强度 <mac>：字符串参数，AP 的 MAC 地址 <channel>：信道号 <scan_type>：Wi-Fi 扫描类型，默认值为：0 0: 主动扫描 1: 被动扫描 <scan_time_min>：每个信道最短扫描时间，单位：毫秒，范围：[0,1500]，如果扫描类型为被动扫描，本参数无效 <scan_time_max>：每个信道最长扫描时间，单位：毫秒，范围：[0,1500]，如果设为 0，固件采用参数默认值，主动扫描为 120 ms，被动扫描为 360 ms <freq_offset>：频偏（保留项目） <freqcal_val>：频率校准值（保留项目） <pairwise_cipher>：成对加密类型 0: None 1: WEP40 2: WEP104 3: TKIP 4: CCMP 5: TKIP and CCMP 6: AES-CMAC-128 7: 未知 <group_cipher>：组加密类型，与 <pairwise_cipher> 参数的枚举值相同 <bgn>：802.11 b/g/n，若 bit 设为 1，则表示使能对应模式，若设为 0，则表示禁用对应模式 bit 0: 是否使能 802.11b 模式 bit 1: 是否使能 802.11g 模式 bit 2: 是否使能 802.11n 模式 <wps>：wps flag 0: 不支持 WPS 1: 支持 WPS 示例 AT+CWLAP="Wi-Fi","ca:d7:19:d8:a6:44",6,0,400,1000 // 寻找指定 SSID 的 AP AT+CWLAP="Wi-Fi" 收到的消息的例子： +CWLAP:(4,"mi2G",-20,"50:4f:3b:32:bc:a6",1,-1,-1,5,3,7,1) +CWLAP:(4,"芝麻知产",-58,"3c:6a:48:91:33:9c",11,-1,-1,4,4,7,0) +CWLAP:(4,"",-58,"3e:6a:48:71:33:9c",11,-1,-1,4,4,7,0) +CWLAP:(4,"",-58,"3e:6a:48:71:35:3d",11,-1,-1,4,4,7,0) +CWLAP:(4,"芝麻知产",-59,"3c:6a:48:91:35:3d",11,-1,-1,4,4,7,0) +CWLAP:(4,"CU_4k4u",-71,"d4:f7:56:c9:f2:64",5,-1,-1,5,3,7,0) +CWLAP:(4,"CU_EgK7",-71,"b8:dd:71:2f:65:33",8,-1,-1,5,3,7,0) +CWLAP:(4,"芝麻知产",-79,"3c:6a:48:91:32:16",11,-1,-1,4,4,7,0) +CWLAP:(4,"",-79,"3e:6a:48:71:32:16",11,-1,-1,4,4,7,0) +CWLAP:(4,"HDKJ-2.4G",-80,"9c:b2:e8:11:7a:e8",1,-1,-1,5,3,7,1) +CWLAP:(4,"芝麻知产",-82,"3c:6a:48:91:32:b5",11,-1,-1,4,4,7,0) +CWLAP:(4,"HDKJ-2.4G",-93,"9c:b2:e8:11:7a:80",11,-1,-1,5,3,7,1) OK 3.USART1调试输出使用的函数名称（如printf_debug或USART1_SendString）： USART1_debug.c中的void USART1_SendString(char *String)函数，例如主函数开始时显示开始信息：USART1_SendString("start\r\n"); 4.最大需要存储的WiFi热点数量：大概15个，考虑到使用的时stm32f103，可能使用数组存储比较好？我不确定使用动态内存是否会产生较坏的结果。我认为wifi信息仅需要SSID、mac地址、保存的密码以及信号强度用来排序即可。

USART1已经有输出用的函数了，其文件中的函数名就对应了其功能。 关于wifi列表查询功能，在esp8266_wifi.c文件中已经实现了一部分： case 4://列出扫描到的WIFI if(AT_Send_Check((uint8_t *)"AT+CWLAP",esp.readData,(uint8_t *)"OK",1,5000)) { connect_step = 5; USART1_SendString("列出扫描到的WIFI\r\n"); } ESP8266_SendDataClean(); break; 不过其通过串口透传的方式将列表直接显示在USART1，并没有对数据进行处理。 对这段数据进行处理时你提到使用strtok，那么使用sscanf是否会更简单或更节省性能？ 同时目前工程仅在开发和测试阶段，触发扫描仅在void AliCloud_Connect(void)中进行一次，在系统重置或上电时按流程出发，你先添加对数据的处理函数，确定函数或逻辑能够正确使用后再修改整个程序的运行逻辑比较好。 我们按功能或函数进行编写、封装和测试，当一部分功能测试能够成功后再修改其他功能。

在此之前，你已经在case4中添加了显示wifi列表的功能，但但还有一些问题，并且未按照信号强度进行排序。在需求中新增了对ssid进行utf8转gbk的要求，你好像新创建了什么文件，但还没有进行修改和测试。由于某种原因，上一次的对话和工程被打断了，我们重新开始。




我现在向你提供esp8266 AT固件中，相关AT指令的说明，以及程序实际通过串口显示的数据，其中时间戳为串口助手自动生成。esp8266原始数据通过串口透传显示。wifi列表仅需要数据中的ssid、mac、信号强度等连接所需的关键数据。
说明：
## [AT+CWLAP](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#wifi-at)：扫描当前可用的 AP[](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#at-cwlap-ap "永久链接至标题")

### 设置命令[](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#id29 "永久链接至标题")

**功能：**

列出符合特定条件的 AP，如指定 SSID、MAC 地址或信道号

**命令：**

AT+CWLAP=[<ssid>,<mac>,<channel>,<scan_type>,<scan_time_min>,<scan_time_max>]

### 执行命令[](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#id30 "永久链接至标题")

**功能：**

列出当前可用的 AP

**命令：**

AT+CWLAP

**响应：**

+CWLAP:(<ecn>,<ssid>,<rssi>,<mac>,<channel>,<freq_offset>,<freqcal_val>,<pairwise_cipher>,<group_cipher>,<bgn>,<wps>)
OK

### 参数[](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#id31 "永久链接至标题")

- **<ecn>**：加密方式
    
    - 0: OPEN
        
    - 1: WEP
        
    - 2: WPA_PSK
        
    - 3: WPA2_PSK
        
    - 4: WPA_WPA2_PSK
        
    - 5: WPA2_ENTERPRISE
        
    - 6: WPA3_PSK
        
    - 7: WPA2_WPA3_PSK
        
    - 8: WAPI_PSK

    - 9: OWE
- **<ssid>**：字符串参数，AP 的 SSID
- **<rssi>**：信号强度
- **<mac>**：字符串参数，AP 的 MAC 地址
- **<channel>**：信道号
- **<scan_type>**：Wi-Fi 扫描类型，默认值为：0
    
    - 0: 主动扫描
        
    - 1: 被动扫描
        
- **<scan_time_min>**：每个信道最短扫描时间，单位：毫秒，范围：[0,1500]，如果扫描类型为被动扫描，本参数无效
    
- **<scan_time_max>**：每个信道最长扫描时间，单位：毫秒，范围：[0,1500]，如果设为 0，固件采用参数默认值，主动扫描为 120 ms，被动扫描为 360 ms
    
- **<freq_offset>**：频偏（保留项目）
    
- **<freqcal_val>**：频率校准值（保留项目）
    
- **<pairwise_cipher>**：成对加密类型
    - 0: None
    - 1: WEP40
    - 2: WEP104
    - 3: TKIP
    - 4: CCMP
    - 5: TKIP and CCMP
    - 6: AES-CMAC-128
    - 7: 未知
- **<group_cipher>**：组加密类型，与 `<pairwise_cipher>` 参数的枚举值相
- **<bgn>**：802.11 b/g/n，若 bit 设为 1，则表示使能对应模式，若设为 0，则表示禁用对应模式
    - bit 0: 是否使能 802.11b 模式
    - bit 1: 是否使能 802.11g 模式
    - bit 2: 是否使能 802.11n 模式
- **<wps>**：wps flag
    - 0: 不支持 WPS
    - 1: 支持 WPS

### 示例[](https://docs.espressif.com/projects/esp-at/zh_CN/latest/esp32/AT_Command_Set/Wi-Fi_AT_Commands.html#id32 "永久链接至标题")

AT+CWLAP="Wi-Fi","ca:d7:19:d8:a6:44",6,0,400,1000

// 寻找指定 SSID 的 AP
AT+CWLAP="Wi-Fi"

串口显示：

[2025-02-18 08:58:36.257]# Cannot open COM port

[2025-02-18 08:58:53.663]# RECV ASCII/208 <<<
USART2 READY!
USART3 READY!
start
ATE0

OK
PASS
关闭回显

OK
PASS

OK
PASS

OK
PASS

OK
PASS

OK
PASS

OK
PASS

OK
SS
AT测试连接正常

OK
PASS
设置多连接模式

[2025-02-18 08:58:55.757]# RECV ASCII/864 <<<
+CWLAP:(4,"mi2G",-26,"50:4f:3b:32:bc:a6",1,-1,-1,5,3,7,1)
+CWLAP:(4,"芝麻知产",-61,"3c:6a:48:91:33:9c",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-61,"3e:6a:48:71:33:9c",11,-1,-1,4,4,7,0)
+CWLAP:(4,"芝麻知产",-63,"3c:6a:48:91:35:3d",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-64,"3e:6a:48:71:35:3d",11,-1,-1,4,4,7,0)
+CWLAP:(4,"CU_4k4u",-78,"d4:f7:56:c9:f2:64",8,-1,-1,5,3,7,0)
+CWLAP:(4,"",-80,"3e:6a:48:71:32:b5",11,-1,-1,4,4,7,0)
+CWLAP:(4,"HDKJ-2.4G",-81,"9c:b2:e8:11:7a:e8",1,-1,-1,5,3,7,1)
+CWLAP:(4,"CU_EgK7",-81,"b8:dd:71:2f:65:33",4,-1,-1,5,3,7,0)
+CWLAP:(4,"芝麻知产",-81,"3c:6a:48:91:32:b5",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-84,"3e:6a:48:71:32:16",11,-1,-1,4,4,7,0)
+CWLAP:(4,"芝麻知产",-86,"3c:6a:48:91:32:16",11,-1,-1,4,4,7,0)
+CWLAP:(4,"芝麻知产",-88,"04:f9:f8:77:1c:84",11,-1,-1,4,4,7,0)

OK
PASS
列出扫描到的WIFI
WIFI DISCONNECT

[2025-02-18 08:58:56.127]# RECV ASCII/47 <<<
WIFI CONNECTED
PASS
连接WIFI

busy p...

[2025-02-18 08:58:56.848]# RECV ASCII/60 <<<
WIFI GOT IP

OK
PASS
查询IP

OK
PASS
设置 MQTT

[2025-02-18 08:58:57.236]# RECV ASCII/78 <<<
+MQTTCONNECTED:0,1,"aiot.zzscholar.com","1883","",1

OK
PASS
连接 MQTT

[2025-02-18 08:58:57.296]# RECV ASCII/26 <<<

OK
PASS
订阅

OK

[2025-02-18 08:58:59.310]# RECV ASCII/22 <<<
10 -> TIME OUT

OK

[2025-02-18 08:58:59.811]# RECV ASCII/260 <<<
+MQTTSUBRECV:0,"v1/devices/me/rpc/request/187",207,{"method":"responseBarcodeInfo","params":{"deviceName":"\\u5de1\\u68c0\\u8f662","code_address":"d6d0b9fa","code_name":"d2f8P7ccbde2b6bebfc5c1a320","code_price":"5.22","code_sn":"6935127803187","status":200}}

[2025-02-18 08:59:01.816]# RECV ASCII/98 <<<
code name!!---->>>d2f8c7ccbde2b6bebfc5c1a320发布
屏幕显示 ---->>>d2f8c7ccbde2b6bebfc5c1a320




















这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.将接收到的MQTT消息中的"code_name"拆分出来，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1

下一步要实现的功能：
1.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。目前在case4中仅通过串口透传输出，并没有存储和处理ESP8266的任何数据。

现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一个功能，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。
这是目前运行时串口输出的所有信息：
[2025-02-18 09:19:57.288]# RECV ASCII/183 <<<
01 -> TIME OUT
ATE0

OK
PASS
关闭回显

OK
SS

OK
PASS

OK
PASS

OK
SS

OK
PASS

OK
SS

OK
PASS
AT测试连接正常

OK
PASS
设置多连接模式

[2025-02-18 09:19:59.372]# RECV ASCII/716 <<<
+CWLAP:(4,"mi2G",-21,"50:4f:3b:32:bc:a6",1,-1,-1,5,3,7,1)
+CWLAP:(4,"芝麻知产",-57,"3c:6a:48:91:33:9c",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-57,"3e:6a:48:71:33:9c",11,-1,-1,4,4,7,0)
+CWLAP:(4,"芝麻知产",-58,"3c:6a:48:91:35:3d",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-59,"3e:6a:48:71:35:3d",11,-1,-1,4,4,7,0)
+CWLAP:(4,"",-74,"3e:6a:48:71:32:b5",11,-1,-1,4,4,7,0)
+CWLAP:(4,"CU_4k4u",-77,"d4:f7:56:c9:f2:64",2,-1,-1,5,3,7,0)
+CWLAP:(4,"CU_EgK7",-77,"b8:dd:71:2f:65:33",4,-1,-1,5,3,7,0)
+CWLAP:(4,"",-81,"06:f9:f8:77:1a:e9",11,-1,-1,4,4,7,0)
+CWLAP:(4,"HDKJ-2.4G",-83,"9c:b2:e8:11:7a:e8",1,-1,-1,5,3,7,1)
+CWLAP:(4,"",-84,"06:f9:f8:77:1c:84",11,-1,-1,4,4,7,0)

OK
PASS
列出扫描到的WIFI
WIFI DISCONNECT

[2025-02-18 09:19:59.768]# RECV ASCII/47 <<<
WIFI CONNECTED
PASS
连接WIFI

busy p...

[2025-02-18 09:20:00.619]# RECV ASCII/60 <<<
WIFI GOT IP

OK
PASS
查询IP

OK
PASS
设置 MQTT

[2025-02-18 09:20:00.843]# RECV ASCII/78 <<<
+MQTTCONNECTED:0,1,"aiot.zzscholar.com","1883","",1

OK
PASS
连接 MQTT

[2025-02-18 09:20:00.899]# RECV ASCII/26 <<<

OK
PASS
订阅

OK

[2025-02-18 09:20:02.914]# RECV ASCII/22 <<<
10 -> TIME OUT

OK

[2025-02-18 09:20:03.924]# RECV ASCII/260 <<<
+MQTTSUBRECV:0,"v1/devices/me/rpc/request/191",207,{"method":"responseBarcodeInfo","params":{"deviceName":"\\u5de1\\u68c0\\u8f662","code_address":"d6d0b9fa","code_name":"d2fPc7ccbde2b6bebfc5c1a320","code_price":"5.22","code_sn":"6935127803187","status":200}}

[2025-02-18 09:20:04.642]# RECV ASCII/260 <<<
+MQTTSUBRECV:0,"v1/devices/me/rpc/request/192",207,{"method":"responseBarcodeInfo","params":{"deviceName":"\\u5de1\\u68c0\\u8f662","code_address":"d6d0b9fa","code_name":"d2f8c7ccbde2b6bebfc5c1a320","code_price":"5.22","code_sn":"6935127803187","status":200}}

[2025-02-18 09:20:05.930]# RECV ASCII/98 <<<
code name!!---->>>d2f8c7ccbde2b6bebfc5c1a320发布
屏幕显示 ---->>>d2f8c7ccbde2b6bebfc5c1a320








这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.接收到的MQTT消息中的"code_name"拆分出来并转化为HEX，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1
5.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。目前在case4中仅通过串口输出。


下一步操作：
将case4中处理wifi列表的逻辑代码打包成函数，提高代码可读性。
阅读理解代码，尝试找出还需优化的地方

现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一步操作，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。















这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.接收到的MQTT消息中的"code_name"拆分出来并转化为HEX，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1
5.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。



现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一步操作，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。

现在功能已经完美实现，程序也能够正常运行。我想在WIFI_LIST方面增加一个可选功能：过滤SSID为中文的WIFI，已知ESP8266输出的SSID都为UTF8格式编码。此功能为可选功能，可通过宏定义来确定功能是否开启，这个功能大概应该添加在之前打包的函数中，不要影响其他正常功能。





这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.接收到的MQTT消息中的"code_name"拆分出来并转化为HEX，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1
5.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。并且可选开启过滤中文SSID功能。



现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一步操作，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。

在代码中，有一些使用strcat()来拼接字符串给MQTT发送JSON的代码，请你将这些使用strcat()的代码修改成使用spintf()的，同时注意转义字符"\""或空格等字符。
还有连续使用USART1_SENDSTRING发送字符串的情况，也使用sprintf拼接字符串后再使用一个USART1_SENDSTRING进行发送，提高代码性能和可读性。
一定要注意代码格式正确，没有语法错误。





这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.接收到的MQTT消息中的"code_name"拆分出来并转化为HEX，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1
5.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。并且可选开启过滤中文SSID功能。



现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一步操作，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。

在之前，你已经完成了以下任务：在代码中，有一些使用strcat()来拼接字符串给MQTT发送JSON的代码，请你将这些使用strcat()的代码修改成使用spintf()的，同时注意转义字符"\""或空格等字符。

现在，请你进行以下任务：还有连续多次重复使用USART1_SENDSTRING发送字符串的情况，确认其功能后，也使用sprintf拼接字符串后再使用一个USART1_SENDSTRING进行发送，提高代码性能和可读性。

一定要注意代码格式正确，没有语法错误。










这是一个嵌入式项目，现在正在按功能或模块进行开发和测试，项目基于STM32F103C8T6，在KEIL V5环境下，使用STM32标准库、C语言进行开发，目前已经编写了部分代码，并实现了一些功能。
以下是目前正在进行和初步实现的功能：
1.USART1连接电脑进行调试，可透传串口数据，直接发送AT指令并接收USART2收到的指令
2.USART2连接ESP8266模块，通过AT指令控制ESP8266扫描、连接WIFI，连接MQTT服务器发布和订阅消息。并通过串口透传直接在USART1显示
3.接收到的MQTT消息中的"code_name"拆分出来并转化为HEX，并保存和输出
4.有一块串口屏用于输出显示，但暂不使用，调试信息先输出在USART1
5.将ESP8266扫描到的wifi列表储存并打印在屏幕上（可先实现通过串口1发生和显示，串口屏先不参与调试）。并且可选开启过滤中文SSID功能。



现在请你读懂并理解现有代码，有一些参数或定义可能没有注释，你可以向我询问。请你实现下一步操作，代码修改时尽量压缩步骤一次性完成，避免多次重复修改。修改代码时按块或函数或功能进行修改。在代码中创建变量、函数或实现复杂逻辑时请你同时写上简单的注释。

在代码中，有一些使用strcat()来拼接字符串给MQTT发送JSON的代码，请你将这些使用strcat()的代码修改成使用spintf()的，同时注意转义字符"\""或空格等字符。
还有连续使用USART1_SENDSTRING发送字符串的情况，也使用sprintf拼接字符串后再使用一个USART1_SENDSTRING进行发送，提高代码性能和可读性。

同时帮我对函数进行注释，如果有不能完全理解的地方，请直接询问我，不要擅自修改。
在此之前你已经完成了修改，但未进行验证，现在编译后有一些报错信息，请你检查和修改。

> C:\Keil_v5\UV4\UV4.exe -r e:\DeepSeek测试\CareBox\Project.uvprojx -j0 -t "Target 1" -o e:\DeepSeek测试\CareBox\.vscode\uv4.log

*** Using Compiler 'V5.06 update 6 (build 750)', folder: 'C:\Keil_v5\ARM\ARMCC\Bin'
Rebuild target 'Target 1'
assembling startup_stm32f10x_md.s...
compiling core_cm3.c...
compiling system_stm32f10x.c...
compiling misc.c...
compiling stm32f10x_adc.c...
compiling stm32f10x_bkp.c...
compiling stm32f10x_can.c...
compiling stm32f10x_cec.c...
compiling stm32f10x_crc.c...
compiling stm32f10x_dac.c...
compiling stm32f10x_dbgmcu.c...
compiling stm32f10x_dma.c...
compiling stm32f10x_exti.c...
compiling stm32f10x_flash.c...
compiling stm32f10x_fsmc.c...
compiling stm32f10x_gpio.c...
compiling stm32f10x_i2c.c...
compiling stm32f10x_iwdg.c...
compiling stm32f10x_pwr.c...
compiling stm32f10x_rcc.c...
compiling stm32f10x_rtc.c...
compiling stm32f10x_sdio.c...
compiling stm32f10x_spi.c...
compiling stm32f10x_tim.c...
compiling stm32f10x_usart.c...
compiling stm32f10x_wwdg.c...
compiling Delay.c...
compiling LED.c...
compiling Key.c...
compiling OLED.c...
compiling main.c...
compiling stm32f10x_it.c...
compiling mqtt.c...
compiling usart2_esp8266.c...
compiling USART1_debug.c...
compiling ESP8266_wifi.c...
API\ESP8266_wifi.c(232): error:  #65: expected a ";"
  {
API\ESP8266_wifi.c(266): warning:  #12-D: parsing restarts here after previous syntax error
      USART1_SendNumber(connect_step, 2);
API\ESP8266_wifi.c(268): error:  #118: a void function may not return a value
      return 1;
API\ESP8266_wifi.c: 1 warning, 2 errors
compiling cJSON.c...
compiling IPS_screen.c...
compiling usart3_screen.c...
".\Objects\Project.axf" - 2 Error(s), 1 Warning(s).
Target not created.
Build Time Elapsed:  00:00:20

 *  终端进程“C:\windows\System32\WindowsPowerShell\v1.0\powershell.exe -Command & c:\Users\Administrator\.vscode\extensions\cl.keil-assistant-1.7.0\bin\Uv4Caller.exe -o e:\DeepSeek测试\CareBox\.vscode\uv4.log --uv4Path C:\Keil_v5\UV4\UV4.exe --prjPath e:\DeepSeek测试\CareBox\Project.uvprojx --targetName 'Target 1' -c '${uv4Path} -r ${prjPath} -j0 -t ${targetName}'”已终止，退出代码: 1。 
 *  终端将被任务重用，按任意键关闭。 