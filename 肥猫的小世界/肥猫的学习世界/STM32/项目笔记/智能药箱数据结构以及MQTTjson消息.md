## 一些宏定义
```c
#define MAXIMUM_MEDICATION_TIME                 //每种药一天最多有多少次服用
#define MAXIMUM_VARIETY_OF_MEDICINES           //最多有几种药
```
## 一、药品信息
### 1. 药品数据结构

#### 药物信息存储的数据结构：

- 用于存储存入药盒中的药物信息

- 主要包含药物信息和处方信息

- 处方就是什么时候吃药、吃多少，处方由用户在新增药品时输入并确定

```c
typedef struct {
    char name[32];          // 药物名称
    char barcode[16];       // 药物条码
    uint8_t id;             //药盒ID 1-8
    // 服用时间，按 [小时, 分钟] 存储，最多支持MAXIMUM_MEDICATION_TIME个时间点
    uint8_t times[MAXIMUM_MEDICATION_TIME][2];
    // 对应的每个时间点服用的数量
    uint8_t doses[MAXIMUM_MEDICATION_TIME];       
    uint16_t total_days;    // 共服用的天数（服药周期）
    uint8_t active_days;    // 服用的天数（吃几天）
    uint8_t pause_days;     // 暂停的天数（停几天）
    uint8_t current_days;   // 当前第几天（现在是吃药的第几天）
    uint8_t shortage;       // 缺药状态：0=正常，1=缺药
    uint8_t consumption;    // 服药状态：0=未服用，1=服用完成
} DrugInfo;                 //药物信息结构体  

DrugInfo Drug[MAXIMUM_VARIETY_OF_MEDICINES]; //创建药物数组

```

### 2.抽象药品数据结构：
#### 数据结构

即`DrugInfo Drug[MAXIMUM_VARIETY_OF_MEDICINES]; `这个数组的抽象为表格。

|     |  药品名  |     药物条码      | 药盒id | 服药时间点 | 服药数量 | 服用天数 | 连续服用天数 | 暂停服用天数 | 当前服用第几天 |
| :-: | :---: | :-----------: | :--: | :---: | :--: | :--: | :----: | :----: | :-----: |
|  1  | 黄连上清片 | 6924168200109 |  1   | 时间点数组 | 数量数组 |  30  |   5    |   2    |    1    |
|  2  |       |               |      |       |      |      |        |        |         |
|  3  |       |               |      |       |      |      |        |        |         |
| ……  |       |               |      |       |      |      |        |        |         |
#### 服药时间点和服药数量：

即

```c
// 药物名称
char name[32];          
// 服用时间
uint8_t times[MAXIMUM_MEDICATION_TIME][2];
// 对应的每个时间点服用的数量
uint8_t doses[MAXIMUM_MEDICATION_TIME];       
```
这三个变量之间的关系

|       | 时间点1 | 数量1 | 时间点2  | 数量2 | 时间点3  | 数量3 | ……  |
| :---: | :--: | :-: | :---: | :-: | :---: | :-: | :-: |
| 黄连上清片 | 8:00 |  2  | 12:30 |  2  | 18:00 |  4  | ……  |
|  ……   |      |     |       |     |       |     |     |

#### 时间点：

即储存时间的方式，将时和分分开储存，在处理数据时更加方便且易读。

|     | 时间点1 | 时间点2 | 时间点3 | 时间点4 | 时间点5 | ……  |
| :-: | :--: | :--: | :--: | :--: | :--: | :-: |
|  时  |  8   |  12  |  18  |  0   |  0   | ……  |
|  分  |  00  |  30  |  00  |  0   |  0   | ……  |



### 3.新增/修改/添加药品时发送的JSON消息

- 当 **添加(新增/补充)药品** 事件发生时，由药箱通过MQTT向服务器发送JSON消息。

- **其中汉语中文使用GBK编码格式！**

以下是JSON消息格式：

```json
{
    "type": "new_drug",         //  事件名  添加药物
    "name": "药品名称",          //  药品名称
    "barcode": "药物条码",      //  药物条码  作为整个系统中药品唯一ID
    "time_count": n,           //服药时间点数量
    "schedule": [              //服药的时间点，n个,有几个数组中就由几个数据
        {"time": "hh:mm", "dose": n},
        {"time": "hh:mm", "dose": n}
    ],
    "total_days": n,                // 共服用的天数（服药周期）
    "active_days": n,               // 服用的天数（吃几天）
    "pause_days": n,                // 暂停的天数（停几天）
    "current_days": n               // 当前第几天（现在是吃药的第几天）
}
```

#### 举个栗子

例子1：
- 药品名:`黄连上清片`
- 药物条码:`6924168200109`
- 服药时间点数量(服药次数):`3`
- 服药时间点1:`8:00`
- 服药数量1:`2`
- 服药时间点2:`12:30`
- 服药数量2:`2`
- 服药时间点3:`18:00`
- 服药数量3:`4`
- 共服药天数:`30`
- 服用天数:`5`
- 停用天数:`2`
- 当前天数(新增时为第1天):`1`

```json
{
    "type": "new_drug",
    "name": "黄连上清片",
    "barcode": "6924168200109",
    "time_count": 3,
    "schedule": [
        {"time": "08:00", "dose": 2},
        {"time": "12:30", "dose": 2},
        {"time": "18:00", "dose": 4}
    ],
    "total_days": 30,
    "active_days": 5,
    "pause_days": 2,
    "current_days": 1
}
```
  上述例子代表，新增药品-黄连上清片，1天吃3次、第1次和第2次吃2片，第三次吃4片，一共要吃1个月，一周吃5天，停药2天，当前是服药的第1天。
  
  



## 二、服药时间轴

- 在修改药品（添加/删除/修改处方）后，刷新当日时间轴 。
- 时间轴表示当天0点至24点，服药节点按时间顺序分布在时间轴上    。
- 系统实时检测当前，当系统时间达到服药节点（或前五分钟）时，触发服药事件。
- 时间轴可按结构体数组形式存储，其中时间轴包含当天服药节点的数量信息，以及服药节点的信息，服药信息。
- 服药时间轴仅在本地生成和储存，后台可用相同思路生成时间轴用于用户在APP中查看，即按一天中的时间顺序查看。

### 1.时间轴抽象图

每天00：00或修改药品信息后重新生成服药时间轴，生成时将药品逐一插入时间轴并新建服药节点，若有相同的服药时间，那么直接在已有服药节点中插入药品信息。

```
时间轴 1月1日 
	0：00
	|
	| <-- 当前时间
	|
	8：00 ---- 黄连上清片*2
	|          盐酸小檗碱片*2
	|
	|
	|
	12:00 ---- 盐酸小檗碱片*2
	|
	|
	12:30 ---- 黄连上清片*2
	|
	|
	|
	|
	18：00 ---- 黄连上清片*4
	|
	|
	|
	24:00
时间轴结束
```

- 每天最多 [药盒数量×药物服用次数] 个节点。
- 

### 2.时间轴数据结构

- 每日或修改处方信息后，重建时间轴
- 每次按药物处方在本地生成，不上传服务器，服务器自行生成
- 时间轴中主要存储每个节点中时间的信息、药物信息和数量信息

- 结构：时间轴结构体、节点结构体数组
- 时间轴结构体：时间轴节点数量、节点结构体数组、药物信息结构体数组
- 节点结构体数组：时间信息、药物信息结构体数组、药物数量信息
- 药物信息结构体：药物名称、药物条码、服药数量


```c
typedef struct {
    // 当前时间节点需要服用的药品名（最多存储 MAXIMUM_VARIETY_OF_MEDICINES 种药品）
    char drug_names[32];
    //条码
    char barcode[16];
	// 数量信息，对应药品的服用剂量，药品名称、条码和剂量一一对应
    uint8_t doses;   
} DrugDoseInfo;


typedef struct {
    uint8_t hour;               // 服用时间：小时 (0-23)
    uint8_t minute;             // 服用时间：分钟 (0-59)
	//时间轴中每个节点的服药信息
	DrugDoseInfo drugs[MAXIMUM_VARIETY_OF_MEDICINES];    
    // 当前时间节点服用的药品种类数量(实际服用的药品种数)本服药节点需要服用多少种药物
    uint8_t drug_count;        
} TimelineNode;                 //时间轴中每个节点的服药信息


typedef struct {
	// 一天最多 MAXIMUM_VARIETY_OF_MEDICINES * MAXIMUM_MEDICATION_TIME 个时间点
	//也就是最多几种药，每种药每天最多吃多少次
    TimelineNode nodes[MAXIMUM_VARIETY_OF_MEDICINES * MAXIMUM_MEDICATION_TIME];
    // 当前时间轴中的有效时间节点数（一天中需要服用多少次药物）
    uint8_t node_count;         
} Timeline;         //时间轴
```

  
  
### 3.抽象结构

- 行：每个节点中的信息
- 列：节点按时间顺序排列

|            | 时间(时) | 时间(分) |    药品名(组)    |           药品条码(组)           | 服药量(组) | 药品种类数量 |
| :--------: | :---: | :---: | :----------: | :-------------------------: | :----: | :----: |
|    节点1     |   8   |  00   | 黄连上清片、盐酸小檗碱片 | 6924168200109、xxxxxxxxxxxxx |  2、2   |   2    |
|    节点2     |  12   |  00   |    盐酸小檗碱片    |        xxxxxxxxxxxxx        |   2    |   1    |
|    节点3     |  12   |  30   |    黄连上清片     |        6924168200109        |   2    |   1    |
|    节点4     |  18   |  00   |    黄连上清片     |        xxxxxxxxxxxxx        |   4    |   1    |
|     ……     |       |       |              |                             |        |        |
| node_count |       |       |              |                             |        |        |



  - 每个节点中，药品名、药品条码、服药数量三个数组的关系
  - 行：数组
  - 列：药品信息

|      |       1       |       2       | ……  | drug_count |
| :--: | :-----------: | :-----------: | :-: | ---------- |
| 药品名  |     黄连上清片     |    盐酸小檗碱片     |     |            |
| 药物条码 | 6924168200109 | xxxxxxxxxxxxx |     |            |
| 服药量  |       2       |       2       |     |            |

  
  
## 三、服药记录（服药轴）

- 服药记录与时间轴按照时间一一对应
- 若时间轴上有某一节点，那么服药轴应该在相同时间有一节点与其对应
- 服药轴主要存储每种药物是否正常取到、服药盒是否被取出并服药
- 取/服药流程是否完成（避免断电未运行等情况）

### 抽象图

- 服药轴与时间轴一一对应
- 记录每种药实际取药数量和状态
- 记录服药盒是否被取出
- 记录服药流程状态

```
时间轴 1月1日                               服药轴 1月1日
	0：00                                      0:00
	|                                          |
	|                                          |
	|                                          |
	8：00 ---- 黄连上清片*2                     8:00 ---- 黄连上清片   计划取药*2
	|          盐酸小檗碱片*2                   |                     实际取药*2
	|                                          |                     取药成功
	|                                          |         盐酸小檗碱片 计划取药*2
	|                                          |                     实际取药*2
	12:00 ---- 盐酸小檗碱片*2                   12:00 --- ……          取药成功
	|                                          |         服药盒已取出
	|                                          |         服药流程完成
	12:30 ---- 黄连上清片*2                     12:30 --- ……
	|                                          |
	|                                          |
	|                                          |
	|                                          |
	18：00 ---- 黄连上清片*4                    18:00 --- ……
	|                                          |
	| <--- 当前时间                             | < --- 当前时间
	|                                          |
	24:00                                      24:00
时间轴结束                                  服药轴结束
```

### 数据结构
- 结构：服药轴结构体、节点结构体数组、节点药物信息数组
- 服药轴结构体：当天服药轴节点数量，以及节点数组构成的服药轴
- 节点结构体数组：服药时间、服药结构体数组、服药种类数量、服药盒状态和流程状态
- 节点药物信息数组：药物名称、药物条码、服药数量、实际数量、取药状态

```c

// 单种药品的取药信息

typedef struct {
    char drug_name[32];       // 药物名称
    char barcode[16];       // 药物条码
    uint8_t expected_doses;   // 计划取药的数量（时间轴中记录的剂量）
    uint8_t actual_doses;     // 实际成功取药的数量
    uint8_t status;           // 取药状态：0=未执行, 1=成功, 2=部分成功, 3=失败
} DrugDoseInfo;

  

// 服药时间节点的记录
typedef struct {
    uint8_t hour;             // 时间点的小时
    uint8_t minute;           // 时间点的分钟
    // 当前时间点涉及的所有药品取药情况（最多 MAXIMUM_VARIETY_OF_MEDICINES 种药）
    DrugDoseInfo drugs[MAXIMUM_VARIETY_OF_MEDICINES];    
    uint8_t drug_count;       // 当前时间点参与的药品数量
    uint8_t medication_box;   // 服药盒状态：0=未取出, 1=已取出, 2=超时未取药
    uint8_t completed;        // 此时间点是否已完成服药流程：0=未完成, 1=已完成
} MedicationRecord;



typedef struct {
	MedicationRecord medication_record[MAXIMUM_VARIETY_OF_MEDICINES * MAXIMUM_MEDICATION_TIME];//服药轴
	uint8_t node_count;//服药轴节点数量
}MedicationRecordLine
```

  
  
### JSON消息


```json
{
    "type": "Medication_Record",               //服药轴信息
    "time": "hh:mm",                           //时间
    "medication_box_status": 1,                //服药盒状态
    "completed": 1,                            //流程状态
    "drug_count": 2,                           //药物数量
    "drugs": [                                 //药物信息
        {
            "name": "xxxx",                    //药品名
            "barcode": "xxxxxxxxxxxxxx",       //药品条码
            "expected_doses": 2,               //计划取药量
            "actual_doses": 2,                 //实际取药量
            "status": 1                        //取药状态
        },
        {
            "name": "xxxxxx",
            "barcode": "xxxxxxxxxxxxxx",
            "expected_doses": 1,
            "actual_doses": 1,
            "status": 1
        }
    ]
}
```

  
  
  
  

## 四、药箱状态

- 用于不间断更新上传传感器和药箱状态数据
- 替代MQTT心跳、连接状态保活

### 数据结构

- 结构：药箱状态结构体、缺药状态结构体
- 药箱状态结构体：温度、湿度、运行状态、错误信息、缺药状态结构体数组
- 缺药状态结构体数组：缺药药盒ID、缺药药物名称、条码

```c
typedef struct {
	// 最多支持MAXIMUM_VARIETY_OF_MEDICINES种药品
	// 存储缺药的药盒号（0表示药盒1，1表示药盒2...）
    uint8_t box_ids;
    char drug_name[32];       // 药物名称
    char barcode[16];       // 药物条码
} DrugShortageInfo;


typedef struct {
    float temperature;            // 当前温度（单位：℃）
    float humidity;               // 当前湿度（单位：%）
    uint8_t error;                // 运行状态标志：0=正常，1=异常
    char error_message[64];       // 错误描述信息（"传感器错误" 等）
    DrugShortageInfo shortage[MAXIMUM_VARIETY_OF_MEDICINES];    // 简单的缺药信息
    uint8_t shortage_count;      // 当前缺药的药盒数量
} CabinetStatus;
```

  
### JSON消息 


```json
{

    "type": "cabinet_status",
    "temperature": 温度值,
    "humidity": 湿度值,
    "status": {
        "error": 0,
        "message": "正常"
    },
    "shortage_count": x,                       //缺药种类数量
    "shortages": [
	    {
			"name": "xxxx",                    //药品名
			"barcode": "xxxxxxxxxxxxxx",       //药品条码
			"box_ids": x                       //药盒ID
		},
		{
			"name": "xxxx",                    //药品名
			"barcode": "xxxxxxxxxxxxxx",       //药品条码
			"box_ids": x                       //药盒ID
		}
	]
}
```

  
  
  
  
## 五、退药

- 退药就是在本地选择这个药品，并删除其数据
- 随后发送MQTT消息，告知服务器从此用户的药品中删除此药品信息
- 退药后重新生成时间轴


### JSON消息
```json
{
    "type": "delete_drug",
    "name": "药品名称",
    "barcode": "药物条码",
    "box_ids": x
}
```

  
  
  
## 六、外出暂停药箱状态
  
- 药箱有暂停使用功能，暂停使用时药箱所有功能均无法使用，仅能查看时间、温湿度信息
- 启用后药箱恢复正常状态
- 外出就是定一段时间的停用，在达到设定的时间后自动恢复使用，同时也可提前恢复使用

### 数据结构

- 结构：盒子状态结构体

```c
typedef struct {
    uint8_t action ;          // 当前操作0，2，1 (对应 "暂停", "外出", "启用")
    uint16_t timestamp[5];       // 操作触发时间 年、月、日、时、分
    // 自动恢复时间 年、月、日、时、分，可选（外出设置时有效）
    uint16_t  resume_time[5];     
} CabinetState;
```

  
### JSON消息

```json
{
    "type": "xxxxxx",        //事件名称 "cabinet_state"
    "action": x,             //状态
    "timestamp": {           //触发时间 年月日时分
        "year": x,
        "month":x,
        "day":x,
        "hour":x,
        "minutes":x
    },
    "resume_time":{           //解除时间 年月日时分
        "year": x,
        "month":x,
        "day":x,
        "hour":x,
        "minutes":x
    }
}
```


#### 例子

##### 暂停
```json
{
    "type": "cabinet_state",        //事件名称 "cabinet_state"
    "action": 0,             //状态 暂停
    "timestamp": {           //触发时间 年月日时分
        "year": 2025,
        "month":1,
        "day":1,
        "hour":8,
        "minutes":0
    },
    "resume_time":{           //解除时间 年月日时分
        "year": 0,
        "month": 0,
        "day": 0,
        "hour": 0,
        "minutes": 0
    }
}
```

  
##### 外出

```json
{
    "type": "cabinet_state",        //事件名称 "cabinet_state"
    "action": 2,             //状态 外出
    "timestamp": {           //触发时间 年月日时分
        "year": 2025,
        "month":1,
        "day":1,
        "hour":8,
        "minutes":0
    },
    "resume_time":{           //解除时间 年月日时分
        "year": 2025,
        "month": 1,
        "day": 2,
        "hour": 12,
        "minutes": 0
    }
}
```


##### 启用

```json
{
    "type": "cabinet_state",        //事件名称 "cabinet_state"
    "action": 1,             //状态 启用
    "timestamp": {           //触发时间 年月日时分
        "year": 0,
        "month":0,
        "day":0,
        "hour":0,
        "minutes":0
    },
    "resume_time":{           //解除时间 年月日时分
        "year": 2025,
        "month": 1,
        "day": 2,
        "hour": 12,
        "minutes": 0
    }
}
```






## 七、查询药品信息(条码)

- 在添加药品过程中，需要扫码添加药品，此时需要向服务器发送药品条码
- 服务器查询完成后，向药箱发送MQTT消息，包含药品信息


#### 上传药品条码

```json
{
	"code_sn":"6935127803187"
}
```

#### 接收药品信息

```json
{
    "method": "responseBarcodeInfo",
    "params": {
        "deviceName": "\\u5de1\\u68c0\\u8f662",
        "code_address": "d6d0b9fa",
        "code_name": "d2f8c7ccbde2b6bebfc5c1a320",
        "code_price": "5.22",
        "code_sn": "6935127803187",
        "status": 200
    }
}
```



