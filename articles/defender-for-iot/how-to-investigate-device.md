---
title: 調查可疑裝置
description: 本操作指南說明如何使用 Defender for IoT，利用 Log Analytics 來調查可疑的 IoT 裝置。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/04/2020
ms.author: mlottner
ms.openlocfilehash: cd7802fa4a88c3b05140d30ab4d8b2bbd4adc8f4
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934654"
---
# <a name="investigate-a-suspicious-iot-device"></a>調查可疑的 IoT 裝置

適用于 IoT 的 Defender 服務警示可清楚指出當 IoT 裝置參與可疑的活動，或是當有裝置遭到入侵時的指示。

在本指南中，請使用所提供的調查建議來協助判斷組織的潛在風險、決定如何進行補救，以及探索預防未來發生類似攻擊的最佳方式。

> [!div class="checklist"]
> * 尋找您的裝置資料
> * 使用 kql 查詢進行調查

## <a name="how-can-i-access-my-data"></a>如何存取我的資料？

根據預設，Defender for IoT 會將您的安全性警示和建議儲存在 Log Analytics 工作區中。 您也可以選擇儲存未經處理的安全性資料。

若要找出您的 Log Analytics 工作區以進行資料儲存體：

1. 開啟 IoT 中樞，
1. 在 [ **安全性**] 底下，選取 [ **設定**]，然後選取 [ **資料收集**]。
1. 變更 Log Analytics 工作區的組態詳細資料。
1. 按一下 [檔案]  。

下列組態，執行下列步驟來存取儲存在 Log Analytics 工作區中的資料：

1. 選取並按一下 IoT 中樞內的「適用于 IoT 的 Defender」警示。
1. 按一下 [進一步調查]****。
1. 選取 [若要查看哪些裝置有此警示，請按一下這裡並檢視 DeviceId 資料行]****。

## <a name="investigation-steps-for-suspicious-iot-devices"></a>可疑 IoT 裝置的調查步驟

若要查看 IoT 裝置的深入解析和原始資料，請移至您的 Log Analytics 工作區 [以存取您的資料](#how-can-i-access-my-data)。

請參閱下面的範例 kql 查詢，以開始調查您裝置上的警示和活動。

### <a name="related-alerts"></a>相關警示

若要了解同一時間前後是否觸發了其他警示，請使用下列 kql 查詢：

  ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityAlert
  | where ExtendedProperties contains device and ResourceId contains tolower(hub)
  | project TimeGenerated, AlertName, AlertSeverity, Description, ExtendedProperties
  ```

### <a name="users-with-access"></a>具有存取權的使用者

若要了解哪些使用者可以存取此裝置，請使用下列 kql 查詢：

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "LocalUsers"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     GroupNames=extractjson("$.GroupNames", EventDetails, typeof(string)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string))
  | summarize FirstObserved=min(TimestampLocal) by GroupNames, UserName
 ```
請使用這項資料來了解︰

- 哪些使用者可以存取此裝置？
- 具有存取權的使用者是否具有預期的許可權等級？

### <a name="open-ports"></a>開啟連接埠

若要找出裝置中目前正在使用或使用的埠，請使用下列 kql 查詢：

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ListeningPorts"
     and extractjson("$.LocalPort", EventDetails, typeof(int)) <= 1024 // avoid short-lived TCP ports (Ephemeral)
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Protocol=extractjson("$.Protocol", EventDetails, typeof(string)),
     LocalAddress=extractjson("$.LocalAddress", EventDetails, typeof(string)),
     LocalPort=extractjson("$.LocalPort", EventDetails, typeof(int)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     RemotePort=extractjson("$.RemotePort", EventDetails, typeof(string))
  | summarize MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), AllowedRemoteIPAddress=makeset(RemoteAddress), AllowedRemotePort=makeset(RemotePort) by Protocol, LocalPort
 ```

請使用這項資料來了解︰

- 裝置上目前作用中的接聽通訊端有哪些？
- 是否應該允許目前作用中的接聽通訊端？
- 是否有任何可疑的遠端位址連線到裝置？

### <a name="user-logins"></a>使用者登入

若要尋找登入裝置的使用者，請使用下列 kql 查詢：

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "Login"
     // filter out local, invalid and failed logins
     and EventDetails contains "RemoteAddress"
     and EventDetails !contains '"RemoteAddress":"127.0.0.1"'
     and EventDetails !contains '"UserName":"(invalid user)"'
     and EventDetails !contains '"UserName":"(unknown user)"'
     //and EventDetails !contains '"Result":"Fail"'
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     UserName=extractjson("$.UserName", EventDetails, typeof(string)),
     LoginHandler=extractjson("$.Executable", EventDetails, typeof(string)),
     RemoteAddress=extractjson("$.RemoteAddress", EventDetails, typeof(string)),
     Result=extractjson("$.Result", EventDetails, typeof(string))
  | summarize CntLoginAttempts=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), CntIPAddress=dcount(RemoteAddress), IPAddress=makeset(RemoteAddress) by UserName, Result, LoginHandler
 ```

請使用查詢結果來了解︰

- 有哪些使用者登入過此裝置？
- 登入的使用者是否應登入？
- 登入過的使用者是從預期還是非預期的 IP 位址進行連線的？

### <a name="process-list"></a>進程清單

若要找出流程清單是否如預期般運作，請使用下列 kql 查詢：

 ```
  let device = "YOUR_DEVICE_ID";
  let hub = "YOUR_HUB_NAME";
  SecurityIoTRawEvent
  | where
     DeviceId == device and AssociatedResourceId contains tolower(hub)
     and RawEventName == "ProcessCreate"
  | project
     TimestampLocal=extractjson("$.TimestampLocal", EventDetails, typeof(datetime)),
     Executable=extractjson("$.Executable", EventDetails, typeof(string)),
     UserId=extractjson("$.UserId", EventDetails, typeof(string)),
     CommandLine=extractjson("$.CommandLine", EventDetails, typeof(string))
  | join kind=leftouter (
     // user UserId details
     SecurityIoTRawEvent
     | where
        DeviceId == device and AssociatedResourceId contains tolower(hub)
        and RawEventName == "LocalUsers"
     | project
        UserId=extractjson("$.UserId", EventDetails, typeof(string)),
        UserName=extractjson("$.UserName", EventDetails, typeof(string))
     | distinct UserId, UserName
  ) on UserId
  | extend UserIdName = strcat("Id:", UserId, ", Name:", UserName)
  | summarize CntExecutions=count(), MinObservedTime=min(TimestampLocal), MaxObservedTime=max(TimestampLocal), ExecutingUsers=makeset(UserIdName), ExecutionCommandLines=makeset(CommandLine) by Executable
```

請使用查詢結果來了解︰

- 裝置上是否執行了任何可疑的處理序？
- 這些處理序是不是由適當的使用者來執行？
- 是否有任何命令列執行包含正確且預期中的引數？

## <a name="next-steps"></a>下一步

在調查裝置並更加了解您的風險之後，請考慮[設定自訂警示](quickstart-create-custom-alerts.md)來改善您的 IoT 解決方案安全性態勢。 如果您還沒有裝置代理程式，請考慮[部署安全性代理程式](how-to-deploy-agent.md)或[變更現有裝置代理程式的組態](how-to-agent-configuration.md)，以改善您的結果。
