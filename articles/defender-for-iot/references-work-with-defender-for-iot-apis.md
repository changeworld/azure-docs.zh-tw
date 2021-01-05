---
title: 使用 Defender for IoT Api
description: 使用外部 REST API 存取感應器和管理主控台所探索到的資料，並使用該資料執行動作。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.topic: reference
ms.service: azure
ms.openlocfilehash: d49aa50b1b8843dfb5c3d32983ff0bb129543bb0
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839422"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>適用于 IoT 的 Defender 感應器和管理主控台 Api

使用外部 REST API 存取感應器和管理主控台所探索到的資料，並使用該資料執行動作。

連接會透過 SSL 來保護。

## <a name="getting-started"></a>開始使用

一般情況下，當您在適用于 IoT 感應器的 Azure Defender 或內部部署管理主控台上使用外部 API 時，您需要產生存取權杖。 您在感應器和內部部署管理主控台上使用的驗證 Api 不需要權杖。

若要產生權杖：

1. 在 [ **系統設定** ] 視窗中，選取 [ **存取權杖**]。
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="顯示 [存取權杖] 按鈕的 [系統設定] 視窗螢幕擷取畫面。":::

2. 選取 [產生新的權杖] 。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="選取按鈕以產生新的權杖。":::

3. 描述新權杖的目的，然後選取 **[下一步]**。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="產生新的權杖，並輸入與其相關聯的整合名稱。":::

4. 存取權杖隨即出現。 複製它，因為它不會再次顯示。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="複製您的存取權杖以進行整合。":::

5. 選取 [完成]。 您所建立的權杖會出現在 [ **存取權杖** ] 對話方塊中。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="具有已填寫權杖之資產權杖對話方塊的螢幕擷取畫面":::

   [已 **使用**] 表示最後一次收到此權杖的外部呼叫。

   如果此權杖的 [已 **使用**] 欄位中顯示 **N/A** ，表示感應器與連線伺服器之間的連接無法運作。

6. 將標題為 **授權** 的 HTTP 標頭新增至您的要求，並將其值設定為您所產生的權杖。

## <a name="sensor-api-specifications"></a>感應器 API 規格

本節說明下列感應器 Api：

- /api/v1/devices

- /api/v1/devices/connections

- /api/v1/devices/cves

- /api/v1/alerts

- /api/v1/events

- /api/v1/reports/vulnerabilities/devices

- /api/v1/reports/vulnerabilities/security

- /api/v1/reports/vulnerabilities/operational

- /api/external/authentication/validation

- /external/authentication/set_password

- /external/authentication/set_password_by_admin

### <a name="retrieve-device-information"></a>擷取裝置資訊

使用此 API 來要求 Defender for IoT 感應器偵測到的所有裝置清單。

#### <a name="apiv1devices"></a>/api/v1/devices

#### <a name="method"></a>方法

**GET**

要求 Defender for IoT 感應器偵測到的所有裝置清單。

#### <a name="query-parameters"></a>查詢參數

- **授權**：僅篩選已授權和未經授權的裝置。

  **範例**：

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表裝置的 JSON 物件陣列。

#### <a name="device-fields"></a>裝置欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **id** | 數值 | 否 | - |
| **ipAddresses** | JSON 陣列 | 是 | IP 位址 (可以是一個以上的位址，以防網際網路位址或具有雙重 Nic 的裝置)  |
| **name** | String | 否 | - |
| **type** | String | 否 | 未知、工程站、PLC、HMI、Historian、網域控制站、資料庫伺服器、無線存取點、路由器、交換器、伺服器、工作站、IP 攝影機、印表機、防火牆、終端機、VPN 閘道、網際網路或多播和廣播 |
| **macAddresses** | JSON 陣列 | 是 | 如果裝置具有雙重 Nic，MAC 位址 (可以是一個以上的位址)  |
| **operatingSystem** | String | 是 | - |
| **engineeringStation** | Boolean | 否 | True 或 False |
| **掃描器** | Boolean | 否 | True 或 False |
| **已授權** | Boolean | 否 | True 或 False |
| **供應商** | String | 是 | - |
| **協定** | JSON 陣列 | 是 | Protocol 物件 |
| **固件** | JSON 陣列 | 是 | 固件物件 |

#### <a name="protocol-fields"></a>通訊協定欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **名稱** | String | 否 |  |
| **位址** | JSON 陣列 | 是 | Master 或 numeric 值 |

#### <a name="firmware-fields"></a>固件欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **串列** | String | 否 | N/A 或實際值 |
| **model** | String | 否 | N/A 或實際值 |
| **firmwareVersion** | Double | 否 | N/A 或實際值 |
| **additionalData** | String | 否 | N/A 或實際值 |
| **moduleAddress** | String | 否 | N/A 或實際值 |
| **架** | String | 否 | N/A 或實際值 |
| **槽** | String | 否 | N/A 或實際值 |
| **address** | String | 否 | N/A 或實際值 |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

### <a name="retrieve-device-connection-information"></a>取得裝置連線資訊

使用此 API 來要求每個裝置的所有連線清單。

#### <a name="apiv1devicesconnections"></a>/api/v1/devices/connections

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查詢參數

如果您未設定查詢參數，則會傳回所有的裝置連接。

**範例**：

`/api/v1/devices/connections`

- **deviceId**：依特定裝置識別碼篩選，以查看其連接。

  **範例**：

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**：從現在開始往後的時間範圍（以分鐘為單位），在這段期間內連接是使用中的。

  **範例**：

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**：僅篩選特定時間之前偵測到的連接 (毫秒、UTC) 。

  **範例**：

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**：僅篩選特定時間之後偵測到的連接 (（以毫秒為單位）（UTC) ）。

  **範例**：

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表裝置連接的 JSON 物件陣列。

#### <a name="fields"></a>欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **firstDeviceId** | 數值 | 否 | - |
| **secondDeviceId** | 數值 | 否 | - |
| **lastSeen** | 數值 | 否 | Epoch (UTC)  |
| **發現** | 數值 | 否 | Epoch (UTC)  |
| **港口** | 數位陣列 | 否 | - |
| **協定** | JSON 陣列 | 否 | 通訊協定欄位 |

#### <a name="protocol-field"></a>通訊協定欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **name** | String | 否 | - |
| **命令** | 字串陣列 | 否 | - |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

### <a name="retrieve-information-on-cves"></a>取得 Cve 的資訊

使用此 API 來要求網路中的裝置上探索到的所有已知 Cve 清單。

#### <a name="apiv1devicescves"></a>/api/v1/devices/cves

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查詢參數

根據預設，此 API 會針對每個 IP 位址提供具有 Cve 的所有裝置 Ip 清單，最高可達100個最高評分的 Cve。

**範例**：

`/api/v1/devices/cves`

- **deviceId**：依特定裝置 IP 位址篩選，最多可在該特定裝置上找到100個最高分的 cve。

  **範例**：

  `/api/v1/devices/<ipAddress>/cves`

- **頂端**：要針對每個裝置 IP 位址取得的最高分 cve 數目。

  **範例**：

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

JSON 物件的陣列，代表在 IP 位址上識別的 Cve。

#### <a name="fields"></a>欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **cveId** | String | 否 | - |
| **址** | String | 否 | IP 位址 |
| **得分** | String | 否 | 0.0-10。0 |
| **attackVector** | String | 否 | 網路、相鄰網路、本機或實體 |
| **description** | String | 否 | - |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

### <a name="retrieve-alert-information"></a>取得警示資訊

使用此 API 來要求 Defender for IoT 感應器偵測到的所有警示清單。

#### <a name="apiv1alerts"></a>/api/v1/alerts

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查詢參數

- **狀態**：僅篩選已處理或未處理的警示。

  **範例**：

  `/api/v1/alerts?state=handled`

- **fromTime**：若要篩選從特定時間建立的警示 (（毫秒），UTC) 。

  **範例**：

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**：若要篩選只在特定時間之前建立的警示（以毫秒為單位） (（UTC) ）。

  **範例**：

  `/api/v1/alerts?toTime=<epoch>`

- **輸入**：依特定類型篩選警示。 要篩選的現有類型：未預期的新裝置，中斷連線。

  **範例**：

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表警示的 JSON 物件陣列。

#### <a name="alert-fields"></a>警示欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **識別碼** | 數值 | 否 | - |
| **time** | 數值 | 否 | Epoch (UTC)  |
| **title** | String | 否 | - |
| **message** | String | 否 | - |
| **severity** | String | 否 | 警告、次要、主要或重大 |
| **發動機** | String | 否 | 通訊協定違規、原則違規、惡意程式碼、異常或操作 |
| **sourceDevice** | 數值 | 是 | 裝置識別碼 |
| **destinationDevice** | 數值 | 是 | 裝置識別碼 |
| **additionalInformation** | 其他資訊物件 | 是 | - |

#### <a name="additional-information-fields"></a>其他資訊欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **description** | String | 否 | - |
| **資訊** | JSON 陣列 | 否 | String |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

### <a name="retrieve-timeline-events"></a>取出時間軸事件

使用此 API 來要求回報給事件時間軸的事件清單。

#### <a name="apiv1events"></a>/api/v1/events

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查詢參數

- **minutesTimeFrame**：從現在開始的時間範圍（以分鐘為單位），這是報告事件的時間範圍。

  **範例**：

  `/api/v1/events?minutesTimeFrame=20`

- **輸入**：依特定類型篩選事件清單。

  **範例**：

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表警示的 JSON 物件陣列。

#### <a name="event-fields"></a>事件欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|--|
| **timestamp** | 數值 | 否 | Epoch (UTC)  |
| **title** | String | 否 | - |
| **severity** | String | 否 | 資訊、通知或警示 |
| **擁有者** | String | 是 | 如果事件是以手動方式建立，此欄位將包含建立事件的使用者名稱 |
| **content** | String | 否 | - |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

### <a name="retrieve-vulnerability-information"></a>取出弱點資訊

使用此 API 來要求每個裝置的弱點評定結果。

#### <a name="apiv1reportsvulnerabilitiesdevices"></a>/api/v1/reports/vulnerabilities/devices

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

JSON 物件的陣列，代表評估的裝置。

裝置物件包含：

- 一般資料

- 評量分數

- 弱點

#### <a name="device-fields"></a>裝置欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **name** | String | 否 | - |
| **ipAddresses** | JSON 陣列 | 否 | - |
| **securityScore** | 數值 | 否 | - |
| **供應商** | String | 是 |  |
| **firmwareVersion** | String | 是 | - |
| **model** | String | 是 | - |
| **isWirelessAccessPoint** | Boolean | 否 | True 或 False |
| **operatingSystem** | 作業系統物件 | 是 | - |
| **漏洞** | 弱點物件 | 是 | - |

#### <a name="operating-system-fields"></a>作業系統欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **名稱** | String | 是 | - |
| **型別** | String | 是 | - |
| **版本** | String | 是 | - |
| **latestVersion** | String | 是 | - |

#### <a name="vulnerabilities-fields"></a>弱點欄位
 
| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **antiViruses** | JSON 陣列 | 是 | 防毒軟體名稱 |
| **plainTextPasswords** | JSON 陣列 | 是 | 密碼物件 |
| **訪問** | JSON 陣列 | 是 | 遠端存取物件 |
| **isBackupServer** | Boolean | 否 | True 或 False |
| **openedPorts** | JSON 陣列 | 是 | 開啟的埠物件 |
| **isEngineeringStation** | Boolean | 否 | True 或 False |
| **isKnownScanner** | Boolean | 否 | True 或 False |
| **cve** | JSON 陣列 | 是 | CVE 物件 |
| **isUnauthorized** | Boolean | 否 | True 或 False |
| **malwareIndicationsDetected** | Boolean | 否 | True 或 False |
| **weakAuthentication** | JSON 陣列 | 是 | 偵測到使用弱式驗證的應用程式 |

#### <a name="password-fields"></a>密碼欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **password** | String | 否 | - |
| **protocol** | String | 否 | - |
| **強度** | String | 否 | 非常弱、弱式、中型或強式 |

#### <a name="remote-access-fields"></a>遠端存取欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **port** | 數值 | 否 | - |
| **運輸** | String | 否 | TCP 或 UDP |
| **客戶** | String | 否 | IP 位址 |
| **clientSoftware** | String | 否 | SSH、VNC、遠端桌面或 Team viewer |

#### <a name="open-port-fields"></a>開啟埠欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **port** | 數值 | 否 | - |
| **運輸** | String | 否 | TCP 或 UDP |
| **protocol** | String | 是 | - |
| **isConflictingWithFirewall** | Boolean | 否 | True 或 False |

#### <a name="cve-fields"></a>CVE 欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **識別碼** | String | 否 | - |
| **得分** | 數值 | 否 | Double |
| **description** | String | 否 | - |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

### <a name="retrieve-security-vulnerabilities"></a>取得安全性弱點

使用此 API 來要求一般弱點評估的結果。 此評量可讓您深入瞭解系統的安全性層級。

這項評量是根據一般網路和系統資訊，而不是特定的裝置評估。

#### <a name="apiv1reportsvulnerabilitiessecurity"></a>/api/v1/reports/vulnerabilities/security

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表評估結果的 JSON 物件。 每個索引鍵可為 null。 否則，它會包含具有不可為 null 索引鍵的 JSON 物件。

### <a name="result-fields"></a>結果欄位

**[索引鍵]**

**unauthorizedDevices**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **address** | String | IP 位址 |
| **name** | String | - |
| **firstDetectionTime** | 數值 | Epoch (UTC)  |
| lastSeen | 數值 | Epoch (UTC)  |

**illegalTrafficByFirewallRules**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **伺服器** | String | IP 位址 |
| **客戶** | String | IP 位址 |
| **port** | 數值 | - |
| **運輸** | String | TCP、UDP 或 ICMP |

**weakFirewallRules**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **sources** | 來源的 JSON 陣列。 每個來源可以有四種格式。 | 「任何」、「ip 位址 (主機) 」、「從 ip 到 ip (範圍) 」、「ip 位址、子網路遮罩 (網路) 」 |
| **目的地** | 目的地的 JSON 陣列。 每個目的地可以有四種格式。 | 「任何」、「ip 位址 (主機) 」、「從 ip 到 ip (範圍) 」、「ip 位址、子網路遮罩 (網路) 」 |
| **港口** | 具有三種格式的埠的 JSON 陣列 | 如果偵測到，則為 "Any"、"埠 (通訊協定，如果偵測到) "、"from port to port (protocol （如果偵測到）) " |

**accessPoints**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **macAddress** | String | MAC 位址 |
| **供應商** | String | 廠商名稱 |
| **址** | String | IP 位址或 N/A |
| **name** | String | 裝置名稱或 N/A |
| **無線** | String | 否、疑似或是 |

**connectionsBetweenSubnets**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **伺服器** | String | IP 位址 |
| **客戶** | String | IP 位址 |

**industrialMalwareIndicators**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **detectionTime** | 數值 | Epoch (UTC)  |
| **alertMessage** | String | - |
| **description** | String | - |
| **設備** | JSON 陣列 | 裝置名稱 | 

**internetConnections**

| 欄位名稱 | 類型 | 值的清單 |
| ---------- | ---- | -------------- |
| **internalAddress** | String | IP 位址 |
| **已授權** | Boolean | [是] 或 [否] | 
| **externalAddresses** | JSON 陣列 | IP 位址 |

#### <a name="response-example"></a>回應範例

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

### <a name="retrieve-operational-vulnerabilities"></a>取出操作弱點

使用此 API 來要求一般弱點評估的結果。 這項評量可讓您深入瞭解您的網路操作狀態。 它是以一般網路和系統資訊為基礎，而不是特定的裝置評估。

#### <a name="apiv1reportsvulnerabilitiesoperational"></a>/api/v1/reports/vulnerabilities/operational

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表評估結果的 JSON 物件。 每個金鑰都包含結果的 JSON 陣列。

#### <a name="result-fields"></a>結果欄位

**[索引鍵]**

**backupServer**

| 欄位名稱 | 類型 | 值的清單 |
|--|--|--|
| **source** | String | IP 位址 |
| **destination** | String | IP 位址 |
| **port** | 數值 | - |
| **運輸** | String | TCP 或 UDP |
| **backupMaximalInterval** | String | - |
| **lastSeenBackup** | 數值 | Epoch (UTC)  |

**ipNetworks**

| 欄位名稱 | 類型 | 值的清單 |
|--|--|--|
| **addresse** s | 數值 | - |
| **網路** | String | IP 位址 |
| **面具** | String | 子網路遮罩 |

**protocolProblems**

| 欄位名稱 | 類型 | 值的清單 |
|--|--|--|
| **protocol** | String | - |
| **位址** | JSON 陣列 | IP 位址 |
| **警報** | String | - |
| **reportTime** | 數值 | Epoch (UTC)  |

**protocolDataVolumes**

| 欄位名稱 | 類型 | 值的清單 |
|--|--|--|
| protocol | String | - |
| 磁碟區 | String | 「磁片區編號 MB」 |

**斷開**

| 欄位名稱 | 類型 | 值的清單 |
|--|--|--|
| **assetAddress** | String | IP 位址 |
| **assetName** | String | - |
| **lastDetectionTime** | 數值 | Epoch (UTC)  |
| **backToNormalTime** | 數值 | Epoch (UTC)  |     

#### <a name="response-example"></a>回應範例

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

### <a name="validate-user-credentials"></a>驗證使用者認證

使用此 API 來驗證 Defender 的 IoT 使用者名稱和密碼。 適用于 IoT 的所有 Defender 使用者角色都可以使用 API。

您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="apiexternalauthenticationvalidation"></a>/api/external/authentication/validation

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="query-parameters"></a>查詢參數

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **username** | String | 否 |
| **password** | String | 否 |

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **成功-msg**：驗證成功

- **失敗-錯誤**：認證驗證失敗

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

### <a name="change-password"></a>變更密碼

使用此 API 可讓使用者變更自己的密碼。 適用于 IoT 的所有 Defender 使用者角色都可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="externalauthenticationset_password"></a>/external/authentication/set_password

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **成功-msg**：已取代密碼

- **失敗-錯誤**：使用者驗證失敗

- **失敗-錯誤**：密碼不符合安全性原則

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>裝置欄位

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **username** | String | 否 |
| **password** | String | 否 |
| **new_password** | String | 否 |

### <a name="user-password-update-by-system-admin"></a>系統管理員的使用者密碼更新

使用此 API 可讓系統管理員變更指定使用者的密碼。 適用于 IoT 系統管理員使用者角色的 Defender 可以與 API 搭配使用。 您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="externalauthenticationset_password_by_admin"></a>/external/authentication/set_password_by_admin

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **成功-msg**：已取代密碼

- **失敗-錯誤**：使用者驗證失敗

- **失敗-錯誤**：使用者不存在

- **失敗-錯誤**：密碼不符合安全性原則

- **失敗-錯誤**：使用者沒有變更密碼的許可權

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>裝置欄位

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **admin_username** | String | 否 |
| **admin_password** | String | 否 |
| **username** | String | 否 |
| **new_password** | String | 否 |

## <a name="on-premises-management-console-api-specifications"></a>內部部署管理主控台 API 規格

本節說明下列內部部署管理主控台 Api：

- **/external/v1/alerts/<UUID>**

- **警示排除 (維護時段)**

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="警示排除視窗，顯示作用中的規則。":::

定義不會傳送警示的條件。 例如，定義和更新在觸發警示時應排除的停止和啟動時間、裝置或子網，或應排除的 IoT 引擎 Defender。 例如，在維護期間，您可能會想要停止傳遞所有警示，但重要裝置上的惡意程式碼警示除外。

您在此處定義的 Api 會在內部部署管理主控台的 [ **警示排除** ] 視窗中顯示為唯讀的排除規則。

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **回應範例**

- **回應：**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password"></a>變更密碼

使用此 API 可讓使用者變更自己的密碼。 適用于 IoT 的所有 Defender 使用者角色都可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

- **/external/authentication/set_password**

#### <a name="user-password-update-by-system-admin"></a>系統管理員的使用者密碼更新

使用此 API 可讓系統管理員變更特定使用者的密碼。 適用于 IoT 的 Defender 系統管理員使用者角色可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

- **/external/authentication/set_password_by_admin**

### <a name="retrieve-device-information"></a>擷取裝置資訊

此 API 會針對已連線至內部部署管理主控台的 IoT 感應器，要求 Defender 偵測到的所有裝置清單。

- **/external/v1/devices**

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表裝置的 JSON 物件陣列。

#### <a name="query-parameters"></a>查詢參數

- **授權**：僅篩選已授權和未經授權的裝置。

- **siteId**：僅篩選與特定網站相關的裝置。

- **zoneId**：僅篩選與特定區域相關的裝置。 [1](#1)

- **>sensorid**：僅篩選特定感應器偵測到的裝置。 [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *您可能沒有網站和區域識別碼。如果是這種情況，請查詢所有裝置以取得網站和區域識別碼。*

#### <a name="query-parameters-example"></a>查詢參數範例

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>裝置欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| sensorId | 數值 | 否 | - |
| **zoneId** | 數值 | 是 | - |
| **siteId** | 數值 | 是 | - |
| **ipAddresses** | JSON 陣列 | 是 | IP 位址 (可以是一個以上的位址，以防網際網路位址或具有雙重 Nic 的裝置)  |
| **name** | String | 否 | - |
| **type** | String | 否 | 未知、工程站、PLC、HMI、Historian、網域控制站、資料庫伺服器、無線存取點、路由器、交換器、伺服器、工作站、IP 攝影機、印表機、防火牆、終端機、VPN 閘道、網際網路或多播和廣播 |
| **macAddresses** | JSON 陣列 | 是 | 如果裝置具有雙重 Nic，MAC 位址 (可以是一個以上的位址)  |
| **operatingSystem** | String | 是 | - |
| **engineeringStation** | Boolean | 否 | True 或 False |
| **掃描器** | Boolean | 否 | True 或 False |
| **已授權** | Boolean | 否 | True 或 False |
| **供應商** | String | 是 | - |
| **通訊協定** | JSON 陣列 | 是 | Protocol 物件 |
| **固件** | JSON 陣列 | 是 | 固件物件 |

#### <a name="protocol-fields"></a>通訊協定欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| Name | String | 否 | - |
| 位址 | JSON 陣列 | 是 | Master 或 numeric 值 |

#### <a name="firmware-fields"></a>固件欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **串列** | String | 否 | N/A 或實際值 |
| **model** | String | 否 | N/A 或實際值 |
| **firmwareVersion** | Double | 否 | N/A 或實際值 |
| **additionalData** | String | 否 | N/A 或實際值 |
| **moduleAddress** | String | 否 | N/A 或實際值 |
| **架** | String | 否 | N/A 或實際值 |
| **槽** | String | 否 | N/A 或實際值 |
| **address** | String | 否 | N/A 或實際值 |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

### <a name="retrieve-alert-information"></a>取得警示資訊

使用此 API 可從內部部署管理主控台取得所有或篩選的警示。

#### <a name="externalv1alerts"></a>/external/v1/alerts

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查詢參數

- **狀態**：僅篩選已處理和未處理的警示。

  **範例**：

  `/api/v1/alerts?state=handled`

- **fromTime**：若要篩選從特定時間建立的警示 (（毫秒），UTC) 。

  **範例**：

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**：若要篩選只在特定時間之前建立的警示（以毫秒為單位） (（UTC) ）。

  **範例**：

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**：探索到警示的網站。 [2](#2)

- **zoneId**：探索到警示的區域。 [2](#2)

- **感應器**：發現警示的感應器。

##### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="2">2</a> *您可能沒有網站和區域識別碼。如果是這種情況，請查詢所有裝置以取得網站和區域識別碼。*

#### <a name="alert-fields"></a>警示欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **識別碼** | 數值 | 否 | - |
| **time** | 數值 | 否 | Epoch (UTC)  |
| **title** | String | 否 | - |
| **message** | String | 否 | - |
| **severity** | String | 否 | 警告、次要、主要或重大 |
| **發動機** | String | 否 | 通訊協定違規、原則違規、惡意程式碼、異常或操作 |
| **sourceDevice** | 數值 | 是 | 裝置識別碼 |
| **destinationDevice** | 數值 | 是 | 裝置識別碼 |
| **additionalInformation** | 其他資訊物件 | 是 | - |

#### <a name="additional-information-fields"></a>其他資訊欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **description** | String | 否 | - |
| **資訊** | JSON 陣列 | 否 | String |

#### <a name="response-example"></a>回應範例

```rest
[

    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Traffic Detected on sensor Interface",
        
        "additionalInformation": null,
        
        "sourceDevice": 0,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808245000,
        
        "sensorId": 1,
        
        "message": "The sensor resumed detecting network traffic on ens224.",
        
        "destinationDevice": 0,
        
        "id": 1,
        
        "severity": "Warning"
    
    },
    
    {
    
        "engine": "Anomaly",
        
        "handled": false,
        
        "title": "Address Scan Detected",
        
        "additionalInformation": null,
        
        "sourceDevice": 4,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808260000,
        
        "sensorId": 1,
        
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        
        "destinationDevice": 0,
        
        "id": 2,
        
        "severity": "Critical"
    
    },
    
    {
    
        "engine": "Operational",
        
        "handled": false,
        
        "title": "Suspicion of Unresponsive MODBUS Device",
        
        "additionalInformation": null,
        
        "sourceDevice": 194,
        
        "zoneId": 1,
        
        "siteId": 1,
        
        "time": 1594808285000,
        
        "sensorId": 1,
        
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        
        "destinationDevice": 0,
        
        "id": 3,
        
        "severity": "Minor"
    
    }
  
]
```

### <a name="qradar-alerts"></a>QRadar 警示

QRadar 與 Defender for IoT 整合可協助您識別 Defender 針對 IoT 產生的警示，並使用這些警示來執行動作。 QRadar 會接收來自 Defender for IoT 的資料，然後與公用 API 內部部署管理主控台元件聯絡。

若要將 Defender 針對 IoT 探索到的資料傳送至 QRadar，請在 Defender for IoT 系統中定義轉送規則，然後選取 [ **遠端支援警示處理** ] 選項。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="編輯轉送規則以符合您的需求。":::

當您在設定轉送規則的過程中選取此選項時，QRadar 中會出現下列其他欄位：

- **UUID**：唯一的警示識別碼，例如1-1555245116250。

- **Site**：探索到警示的網站。

- **區域**：探索到警示的區域。

傳送至 QRadar 的承載範例：

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/ &lt; UUID&gt;

#### <a name="method"></a>方法

**PUT**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-content"></a>要求內容

JSON 物件，代表要在包含 UUID 的警示上執行的動作。

#### <a name="action-fields"></a>動作欄位

| 名稱 | 類型 | Nullable | 值的清單 |
|--|--|--|--|
| **action** | String | 否 | 控制碼或 handleAndLearn |

#### <a name="request-example"></a>要求範例

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

代表裝置的 JSON 物件陣列。

#### <a name="response-fields"></a>回應欄位


| 名稱 | 類型 | Nullable | 描述 |
|--|--|--|--|
| **內容/錯誤** | String | 否 | 如果要求成功，則會顯示 [內容] 屬性。 否則會出現錯誤屬性。 |

#### <a name="possible-content-values"></a>可能的內容值

| 狀態碼 | 內容值 | 描述 |
|--|--|--|
| 200 | 警示更新要求已成功完成。 | 更新要求已順利完成。 沒有批註。 |
| 200 | 警示已處理 (**控制碼**) 。 | 警示已在收到警示的控制碼要求時處理。<br />警示仍保持 **處理**。 |
| 200 | 警示已處理，並已 (**handleAndLearn**) 學習。 | 收到 **handleAndLearn** 的要求時，已處理並瞭解警示。<br />警示會維持在 **handledAndLearn** 狀態中。 |
| 200 | 警示已處理 () **處理** 。<br />處理並瞭解在警示上執行 (**handleAndLearn**) 。 | 收到 **handleAndLearn** 的要求時，已處理警示。<br />警示會變成 **handleAndLearn**。 |
| 200 | 警示已處理，並已 (**handleAndLearn**) 學習。 已忽略控制碼要求。 | 收到處理警示的要求時，已 **handleAndLearn** 警示。 警示會保持 **handleAndLearn**。 |
| 500 | 不正確動作。 | 傳送的動作不是對警示執行的有效動作。 |
| 500 | 發生未預期的錯誤。 | 發生意外錯誤。 若要解決此問題，請聯絡技術支援人員。 |
| 500 | 無法執行要求，因為找不到此 UUID 的警示。 | 在系統中找不到指定的警示 UUID。 |

#### <a name="response-example"></a>回應範例

**成功**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**失敗**

```rest
{
    "error": "Invalid action"
}
```

### <a name="alert-exclusions-maintenance-window"></a>警示排除 (維護時段) 

定義不會傳送警示的條件。 例如，定義和更新在觸發警示時應排除的停止和啟動時間、裝置或子網，或應排除的 IoT 引擎 Defender。 例如，在維護期間，您可能會想要停止警示傳遞所有警示，但重要裝置上的惡意程式碼警示除外。

您在此處定義的 Api 會在內部部署管理主控台的 [ **警示排除** ] 視窗中顯示為唯讀的排除規則。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="警示排除範圍視窗，其中顯示所有排除規則的清單。 ":::

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

#### <a name="method---post"></a>方法-POST

#### <a name="query-parameters"></a>查詢參數

- **ticketId**：定義使用者系統中的維護票證識別碼。

- **ttl**：定義 ttl (存留時間) ，也就是維護時段的持續時間（以分鐘為單位）。 在此參數定義的一段時間之後，系統會自動開始傳送警示。

- **引擎**：定義要在維護過程中隱藏警示的安全性引擎：

   - 異常

   - 惡意 軟體

   - 操作

   - POLICY_VIOLATION

   - PROTOCOL_VIOLATION

- **sensorIds**：定義 IoT 感應器在維護過程中隱藏警示的來源。 它是從/api/v1/appliances 抓取的相同識別碼 (取得) 。

- **子網**：定義要在維護過程中隱藏警示的子網。 子網會以下列格式傳送： 192.168.0.0/16。

#### <a name="error-codes"></a>錯誤碼

- **201 (建立)**：已成功完成動作。

- **400 (錯誤的要求)**：在下列情況下會出現：

   - **Ttl** 參數不是數值或不是正數。

   - **子網** 參數是使用錯誤格式來定義。

   - 遺漏 **ticketId** 參數。

   - **引擎** 參數與現有的安全性引擎不相符。

- **404 (找不到)**：其中一個感應器不存在。

- **409 (衝突)**：票證識別碼已連結到另一個開啟的維護視窗。

- **500 (內部伺服器錯誤)**：任何其他未預期的錯誤。

> [!NOTE]
> 請確定票證識別碼未連結至現有的開啟視窗。 產生的排除規則如下：維護-{token 名稱}-{ticket ID}。

#### <a name="method---put"></a>方法-PUT

藉由變更 **ttl** 參數，可讓您在開始維護程式之後更新維護期間。 新的持續時間定義會覆寫前一個。

當您想要設定比目前設定的持續時間更長的持續時間時，這個方法很有用。

#### <a name="query-parameters"></a>查詢參數

- **ticketId**：定義使用者系統中的維護票證識別碼。

- **ttl**：定義視窗的持續時間（以分鐘為單位）。

#### <a name="error-code"></a>錯誤碼

- **200 (確定)**：動作已成功完成。

- **400 (錯誤的要求)**：在下列情況下會出現：

   - **Ttl** 參數不是數值或不是正數。

   - 遺漏 **ticketId** 參數。

   - 遺漏 **ttl** 參數。

- **404 (找不到)**：票證識別碼未連結至開啟的維護視窗。

- **500 (內部伺服器錯誤)**：任何其他未預期的錯誤。

> [!NOTE]
> 請確定票證識別碼已連結至現有的開啟視窗。

#### <a name="method---delete"></a>方法-刪除

關閉現有的維護視窗。

#### <a name="query-parameters"></a>查詢參數

- **ticketId**：記錄使用者系統中的維護票證識別碼。

#### <a name="error-code"></a>錯誤碼

- **200 (確定)**：動作已成功完成。

- **400 (錯誤的要求)**：缺少 **ticketId** 參數。

- **404 (找不到)**：票證識別碼未連結至開啟的維護視窗。

- **500 (內部伺服器錯誤)**：任何其他未預期的錯誤。

> [!NOTE]
> 請確定票證識別碼已連結至現有的開啟視窗。

#### <a name="method---get"></a>方法-GET

取得在維護期間于系統中執行之所有開啟、關閉和更新動作的記錄。 您只能針對過去使用中的維護期間抓取記錄，並將其關閉。

#### <a name="query-parameters"></a>查詢參數

- **fromDate**：從預先定義的日期和之後篩選記錄。 格式為2019-12-30。

- **toDate**：將記錄篩選到預先定義的日期。 格式為2019-12-30。

- **ticketId**：篩選與特定票證識別碼相關的記錄。

- **tokenName**：篩選與特定標記名稱相關的記錄。

#### <a name="error-code"></a>錯誤碼

- **200 (確定)**：動作已成功完成。

- **400 (錯誤的要求)**：日期格式錯誤。

- **204 (沒有內容)**：沒有可顯示的資料。

- **500 (內部伺服器錯誤)**：任何其他未預期的錯誤。

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

JSON 物件的陣列，表示維護時間範圍作業。

#### <a name="response-structure"></a>回應結構

| 名稱 | 類型 | 註解 | Nullable |
|--|--|--|--|
| **dateTime** | String | 範例： "2012-04-23T18：25： 43.511 Z" | 不可以 |
| **ticketId** | String | 範例： "9a5fe99c-d914-4bda-9332-307384fe40bf" | 不可以 |
| **tokenName** | String | - | 不可以 |
| **發動機** | 字串陣列 | - | 是 |
| **sensorIds** | 字串陣列 | - | 是 |
| **網** | 字串陣列 | - | 是 |
| **Ttl** | 數值 | - | 是 |
| **operationType** | String | 值為 "OPEN"、"UPDATE" 和 "CLOSE" | 不可以 |

### <a name="authenticate-user-credentials"></a>驗證使用者認證

使用此 API 來驗證使用者認證。 適用于 IoT 的所有 Defender 使用者角色都可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="externalauthenticationvalidation"></a>/external/authentication/validation

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **Success – msg**：驗證成功

- **失敗-錯誤**：認證驗證失敗

#### <a name="device-fields"></a>裝置欄位

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **username** | String | 否 |
| **password** | String | 否 |

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

### <a name="change-password"></a>變更密碼

使用此 API 可讓使用者變更自己的密碼。 適用于 IoT 的所有 Defender 使用者角色都可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="externalauthenticationset_password"></a>/external/authentication/set_password

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **成功-msg**：已取代密碼

- **失敗-錯誤**：使用者驗證失敗

- **失敗-錯誤**：密碼不符合安全性原則

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>裝置欄位

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **username** | String | 否 |
| **password** | String | 否 |
| **new_password** | String | 否 |

### <a name="user-password-update-by-system-admin"></a>系統管理員的使用者密碼更新

使用此 API 可讓系統管理員變更指定使用者的密碼。 適用于 IoT 的 Defender 系統管理員使用者角色可以使用 API。 您不需要 Defender for IoT 存取權杖就能使用此 API。

#### <a name="externalauthenticationset_password_by_admin"></a>/external/authentication/set_password_by_admin

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>要求類型

**JSON**

#### <a name="request-example"></a>要求範例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>回應類型

**JSON**

#### <a name="response-content"></a>回應內容

具有操作狀態詳細資料的訊息字串：

- **成功-msg**：已取代密碼

- **失敗-錯誤**：使用者驗證失敗

- **失敗-錯誤**：使用者不存在

- **失敗-錯誤**：密碼不符合安全性原則

- **失敗-錯誤**：使用者沒有變更密碼的許可權

#### <a name="response-example"></a>回應範例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>裝置欄位

| **名稱** | **型別** | **可為 Null** |
|--|--|--|
| **admin_username** | String | 否 |
| **admin_password** | String | 否 |
| **username** | String | 否 |
| **new_password** | String | 否 |

## <a name="see-also"></a>另請參閱
[調查裝置清查](how-to-investigate-sensor-detections-in-a-device-inventory.md) 
 中的感應器偵測[調查裝置清查中的所有企業感應器](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)偵測
