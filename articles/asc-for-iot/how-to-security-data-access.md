---
title: 存取安全性 & 建議資料
description: 瞭解如何在使用適用于 IoT 的 Azure 資訊安全中心時存取您的安全性警示和建議資料。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: fbd96ddd-cd9f-48ae-836a-42aa86ca222d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: bbea0accc79cafb6fea3f1438a71250dc02f4d62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311013"
---
# <a name="access-your-security-data"></a>存取您的安全性資料

適用于 IoT 的 Azure 資訊安全中心會將安全性警示、建議和未經處理的安全性資料（如果您選擇儲存）儲存在 Log Analytics 工作區中。

## <a name="log-analytics"></a>Log Analytics

若要設定使用的 Log Analytics 工作區：

1. 開啟 IoT 中樞。
1. 按一下 [**安全性**] 區段下的 [**總覽**] 分頁
1. 按一下 [**設定**]，並變更 Log Analytics 工作區設定。

若要在設定之後存取 Log Analytics 工作區中的警示和建議：

1. 選擇 IoT Azure 資訊安全中心中的警示或建議。
1. 按一下 [**進一步調查**]，然後按一下**以查看哪些裝置具有此警示，請按一下這裡並查看 DeviceId 資料行**。

如需從 Log Analytics 查詢資料的詳細資訊，請參閱[在 Log analytics 中開始使用查詢](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全性警示

安全性警示會儲存在 Log Analytics 工作區中的_AzureSecurityOfThings SecurityAlert_資料表中，其設定適用于 IoT 解決方案的 Azure 資訊安全中心。

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

| TimeGenerated           | IoTHubId                                                                                                       | DeviceId      | AlertSeverity | DisplayName                           | Description                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18：10：29.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 暴力密碼破解攻擊成功           | 裝置上的暴力密碼破解攻擊已成功        |    {"完整來源位址"： "[ \" 10.165.12.18： \" ]"，"使用者名稱"： "[ \" \" ]"，"DeviceId"： "IoT-裝置-Linux"}                                                                       |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 裝置上的本機登入成功      | 偵測到裝置的本機登入成功     | {"遠端位址"： "？"，"遠端埠"： ""，"本機埠"： ""，"Login Shell"： "/bin/su"，"登入進程 Id"： "28207"，"使用者名稱"： "攻擊者"，"DeviceId"： "IoT-Linux"} |
| 2018-11-19T12：40：31.000 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 裝置上的本機登入嘗試失敗  | 偵測到對裝置的本機登入嘗試失敗 |    {"遠端位址"： "？"，"遠端埠"： ""，"本機埠"： ""，"Login Shell"： "/bin/su"，"登入進程 Id"： "22644"，"使用者名稱"： "攻擊者"，"DeviceId"： "IoT-Linux"} |

### <a name="device-summary"></a>裝置摘要

取得過去一周偵測到的相異安全性警示數目，依 IoT 中樞、裝置、警示嚴重性、警示類型分組。

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 暴力密碼破解攻擊成功           | 9   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中        | 裝置上的本機登入嘗試失敗  | 242 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 裝置上的本機登入成功      | 31  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中        | 加密硬幣 Miner                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中樞摘要

選取過去一周內有警示的不同裝置數，依 IoT 中樞、警示嚴重性、警示類型

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | 高          | 暴力密碼破解攻擊成功           | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | 中        | 裝置上的本機登入嘗試失敗  | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | 高          | 裝置上的本機登入成功      | 1          |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | 中        | 加密硬幣 Miner                     | 1          |

## <a name="security-recommendations"></a>安全性建議

安全性建議會儲存在 Log Analytics 工作區中的_AzureSecurityOfThings SecurityRecommendation_資料表中，其設定適用于 IoT 解決方案的 Azure 資訊安全中心。

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

| TimeGenerated | IoTHubId | DeviceId | RecommendationSeverity | RecommendationState | RecommendationDisplayName | Description | RecommendationAdditionalData |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10：21：06.060 |    /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中 | Active | 在輸入鏈中找到寬鬆的防火牆規則 | 已找到防火牆中的規則，其中包含廣泛 IP 位址或埠的寬鬆模式 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |
| 2019-03-22T10：50：27.237 | /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中 | Active | 在輸入鏈中找到寬鬆的防火牆規則 | 已找到防火牆中的規則，其中包含廣泛 IP 位址或埠的寬鬆模式 | {"Rules"： "[{ \" SourceAddress \" ： \" \" ， \" SourcePort \" ： \" \" ， \" DestinationAddress \" ： \" \" ， \" DestinationPort \" ： \" 1337 \" }]"} |

### <a name="device-summary"></a>裝置摘要

取得相異的 active security 建議數目，依 IoT 中樞、裝置、建議嚴重性和類型分組。

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
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 2   |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中        | 1 |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 高          | 1  |
| /subscriptions/<subscription_id>/resourceGroups/<resource_group>/Providers/microsoft.devices/iothubs/iot-hub-name <iot_hub> | <device_name> | 中        | 4   |

## <a name="next-steps"></a>後續步驟

- 閱讀適用于 IoT 的 Azure 資訊安全中心[總覽](overview.md)
- 瞭解 IoT[架構](architecture.md)的 Azure 資訊安全中心
- 瞭解及探索[IoT 警示的 Azure 資訊安全中心](concept-security-alerts.md)
- 瞭解及探索[IoT 推薦 Azure 資訊安全中心](concept-recommendations.md)
