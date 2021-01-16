---
title: 存取安全性 & 建議資料
description: 瞭解如何在使用 Defender for IoT 時存取您的安全性警示和建議資料。
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
ms.openlocfilehash: 495f9d568760421c7f42df3acf74217c15b01a05
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98246334"
---
# <a name="access-your-security-data"></a>存取您的安全性資料

適用于 IoT 的 Defender 會儲存安全性警示、建議和未經處理的安全性資料 (如果您選擇將其儲存) 在 Log Analytics 工作區中。

## <a name="log-analytics"></a>Log Analytics

設定使用的 Log Analytics 工作區：

1. 開啟 IoT 中樞。
1. 在 [**安全性**] 區段下，按一下 [**設定**] 分頁。
1. 按一下 [ **資料收集**]，然後變更您的 Log Analytics 工作區設定。

若要在設定之後存取 Log Analytics 工作區中的警示和建議：

1. 為 IoT 選擇 Defender 中的警示或建議。
1. 按一下 [ **進一步調查**]，然後按一下 **以查看哪些裝置有此警示，請按一下這裡並查看 DeviceId 資料行**。

如需從 Log Analytics 查詢資料的詳細資訊，請參閱 [開始使用 Log analytics 中的查詢](../azure-monitor/log-query/get-started-queries.md)。

## <a name="security-alerts"></a>安全性警示

安全性警示會儲存在設定為適用于 Defender for IoT 解決方案的 Log Analytics 工作區中的 _AzureSecurityOfThings. SecurityAlert_ 資料表。

我們提供了一些實用的查詢，可協助您開始探索安全性警示。

### <a name="sample-records"></a>範例記錄

選取幾個隨機記錄

```
// Select a few random records
//
SecurityAlert
| project
    TimeGenerated,
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName,
    Description,
    ExtendedProperties
| take 3
```

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | 說明                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18：10：29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 暴力密碼破解攻擊成功           | 裝置上的暴力密碼破解攻擊成功        |    {"Full Source Address"： "[ \" 10.165.12.18： \" ]"，"User Names"： "[ \" \" ]"，"DeviceId"： "IoT-Device-Linux"}                                                                       |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上的本機登入成功      | 偵測到裝置的本機登入成功     | {"Remote Address"： "？"、"Remote Port"： ""、"Local Port"： ""、"Login Shell"： "/bin/su"、"Login Process Id"： "28207"、"User Name"： "攻擊者"、"DeviceId"： "IoT-Device-Linux"} |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上的本機登入嘗試失敗  | 偵測到裝置的本機登入嘗試失敗 |    {"Remote Address"： "？"、"Remote Port"： ""、"Local Port"： ""、"Login Shell"： "/bin/su"、"Login Process Id"： "22644"、"User Name"： "攻擊者"、"DeviceId"： "IoT-Device-Linux"} |

### <a name="device-summary"></a>裝置摘要

取得過去一周內偵測到的相異安全性警示數目，依 IoT 中樞、裝置、警示嚴重性、警示類型分組。

```
// Get the number of distinct security alerts detected in the last week, grouped by
//   IoT hub, device, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| summarize Cnt=dcount(SystemAlertId) by
    IoTHubId=ResourceId,
    DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"]),
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 暴力密碼破解攻擊成功           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 裝置上的本機登入嘗試失敗  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上的本機登入成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 加密硬幣 Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中樞摘要

選取過去一周內有警示的相異裝置數、IoT 中樞、警示嚴重性、警示類型

```
// Select number of distinct devices which had alerts in the last week, by
//   IoT hub, alert severity, alert type
//
SecurityAlert
| where TimeGenerated > ago(7d)
| extend DeviceId=tostring(parse_json(ExtendedProperties)["DeviceId"])
| summarize CntDevices=dcount(DeviceId) by
    IoTHubId=ResourceId,
    AlertSeverity,
    DisplayName
```

| IoTHubId                                                                                                       | AlertSeverity | DisplayName                           | CntDevices |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------------------------------|------------|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 暴力密碼破解攻擊成功           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 裝置上的本機登入嘗試失敗  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 高          | 裝置上的本機登入成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | 中        | 加密硬幣 Miner                     | 1          |

## <a name="security-recommendations"></a>安全性建議

安全性建議儲存在針對適用于 Defender for IoT 解決方案所設定之 Log Analytics 工作區的 _AzureSecurityOfThings SecurityRecommendation_ 資料表中。

我們提供了一些實用的查詢，可協助您開始探索安全性建議。

### <a name="sample-records"></a>範例記錄

選取幾個隨機記錄

```
// Select a few random records
//
SecurityRecommendation
| project
    TimeGenerated,
    IoTHubId=AssessedResourceId,
    DeviceId,
    RecommendationSeverity,
    RecommendationState,
    RecommendationDisplayName,
    Description,
    RecommendationAdditionalData
| take 2
```

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | 說明 | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10：21：06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | 使用中 | 在輸入鏈中找到寬鬆的防火牆規則 | 在防火牆中找到一個規則，其中包含廣泛 IP 位址或埠的寬鬆模式 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |
| 2019-03-22T10：50：27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中 | 使用中 | 在輸入鏈中找到寬鬆的防火牆規則 | 在防火牆中找到一個規則，其中包含廣泛 IP 位址或埠的寬鬆模式 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |

### <a name="device-summary"></a>裝置摘要

取得依 IoT 中樞、裝置、建議嚴重性和類型分組的相異有效安全性建議數目。

```
// Get the number of distinct active security recommendations, grouped by by
//   IoT hub, device, recommendation severity and type
//
SecurityRecommendation
| extend IoTHubId=AssessedResourceId
| summarize CurrentState=arg_max(RecommendationState, DiscoveredTimeUTC) by IoTHubId, DeviceId, RecommendationSeverity, RecommendationDisplayName
| where CurrentState == "Active"
| summarize Cnt=count() by IoTHubId, DeviceId, RecommendationSeverity
```

| IoTHubId                                                                                                       | DeviceId      | RecommendationSeverity | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Devices/IotHubs/<iot_hub> | <device_name> | 中        | 4   |

## <a name="next-steps"></a>後續步驟

- 閱讀適用于 IoT 的 Defender [總覽](overview.md)
- 瞭解適用于 IoT 的 Defender [架構](architecture.md)
- 瞭解及探索 [適用于 IoT 的 Defender 警示](concept-security-alerts.md)
- 瞭解及探索 [適用于 IoT 的 Defender 建議](concept-recommendations.md)