---
title: 使用 Azure IoT 安全中心訪問資料*微軟文檔
description: 瞭解如何在使用 Azure IoT 安全中心時訪問安全警報和建議資料。
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
ms.openlocfilehash: 3ddd9b2c8373746a65cd78f0a81b60d097cd9f38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597174"
---
# <a name="access-your-security-data"></a>訪問您的安全資料 

IoT 的 Azure 安全中心在日誌分析工作區中存儲安全警報、建議和原始安全資料（如果選擇保存）。

## <a name="log-analytics"></a>Log Analytics

要配置使用日誌分析工作區：

1. 開啟 IoT 中樞。
1. 按一下 **"安全**"部分下的 **"概述**"邊欄
2. 按一下 **"設置"** 並更改日誌分析工作區配置。

要在配置後訪問日誌分析工作區中的警報和建議，請執行以下操作：

1. 在 IoT 的 Azure 安全中心中選擇警報或建議。 
2. 按一下**進一步調查**，然後按一下"**查看哪些設備具有此警報"，請按一下此處並查看 DeviceId 列**。

有關從日誌分析查詢資料的詳細資訊，請參閱[在日誌分析中開始查詢](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全性警示

安全警報存儲在為 IoT 解決方案的 Azure 安全中心配置的日誌分析工作區中的_Azure 安全安全警報_表中。

我們提供了許多有用的查詢，以説明您開始探索安全警報。

### <a name="sample-records"></a>示例記錄

選擇幾個隨機記錄

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

| TimeGenerated           | IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | 描述                                             | ExtendedProperties                                                                                                                                                             |
|-------------------------|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-11-18T18：10：29.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 野蠻武力攻擊成功           | 對設備的暴力攻擊成功        |    •\""完整源位址"："10.165.12.18："\"\"使用者名"："\"，"\"\"設備 Id"："IoT-Device-Linux" |                                                                       |
| 2018-11-19T12：40：31.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 設備上成功本地登錄      | 檢測到設備成功本地登錄     | • "遠端位址"："？"，"遠端埠"：""、"本地埠"：""、"登錄外殼"："/bin/su"，"登錄進程 ID"："28207"，"使用者名"："攻擊者"、"設備 Id"："IoT-Device-Linux" | |
| 2018-11-19T12：40：31.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 設備上的本地登錄嘗試失敗  | 檢測到設備的本地登錄嘗試失敗 |  • "遠端位址"："？"，"遠端埠"：""、"本地埠"：""、"登錄外殼"："/bin/su"，"登錄進程 ID"："22644"、"使用者名"："攻擊者"、"設備 Id"："IoT-Device-Linux" | |

### <a name="device-summary"></a>設備摘要

獲取上周檢測到的不同安全警報數，按 IoT 中心、設備、警示嚴重性、警報類型分組。

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

| IoTHubId                                                                                                       | deviceId      | AlertSeverity | DisplayName                           | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|---------------|---------------------------------------|-----|
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 野蠻武力攻擊成功           | 9   |   
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 設備上的本地登錄嘗試失敗  | 242 |    
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 設備上成功本地登錄      | 31  |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 加密硬幣礦工                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

通過 IoT 中心、警示嚴重性、警報類型選擇上周警報的多個不同設備

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
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 高          | 野蠻武力攻擊成功           | 1          |    
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 中        | 設備上的本地登錄嘗試失敗  | 1          | 
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 高          | 設備上成功本地登錄      | 1          |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 中        | 加密硬幣礦工                     | 1          |

## <a name="security-recommendations"></a>安全性建議

安全建議存儲在為 IoT 解決方案的 Azure 安全中心配置的日誌分析工作區中的_Azure 安全資訊.安全建議_表中。

我們提供了許多有用的查詢，以説明您開始探索安全建議。

### <a name="sample-records"></a>示例記錄

選擇幾個隨機記錄

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
    
| TimeGenerated | IoTHubId | deviceId | 建議嚴重性 | 建議國 | 推薦顯示名稱 | 描述 | 建議附加資料 |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10：21：06.060 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中 | Active | 在輸入鏈中找到允許的防火牆規則 | 在防火牆中已發現一條規則，其中包含各種 IP 位址或埠的允許模式 | {"規則\""："]源位址\"：\"\"\"、\"源\"\"埠\"：\"\"\"、\"目的地\"位址\"： 、\"目的地埠 ： 1337 []] |
| 2019-03-22T10：50：27.237 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中 | Active | 在輸入鏈中找到允許的防火牆規則 | 在防火牆中已發現一條規則，其中包含各種 IP 位址或埠的允許模式 | {"規則\""："]源位址\"：\"\"\"、\"源\"\"埠\"：\"\"\"、\"目的地\"位址\"： 、\"目的地埠 ： 1337 []] |

### <a name="device-summary"></a>設備摘要

獲取按 IoT 中心、設備、建議嚴重性和類型分組的不同活動安全建議的數量。

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

| IoTHubId                                                                                                       | deviceId      | 建議嚴重性 | Count |
|----------------------------------------------------------------------------------------------------------------|---------------|------------------------|-----|
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 2   |    
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 1 |  
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 1  |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 4   |


## <a name="next-steps"></a>後續步驟

- 閱讀 Azure 安全中心，瞭解 IoT[概述](overview.md)
- 瞭解適用于 IoT[體系結構](architecture.md)的 Azure 安全中心
- 瞭解並探索[適用于 IoT 警報的 Azure 安全中心](concept-security-alerts.md)
- 瞭解並探索[Azure 安全中心，瞭解 IoT 建議](concept-recommendations.md)
