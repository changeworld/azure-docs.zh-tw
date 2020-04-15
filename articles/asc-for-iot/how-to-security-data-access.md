---
title: 存取安全&建議資料
description: 瞭解如何在使用 Azure IoT 安全中心時訪問安全警報和建議數據。
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311013"
---
# <a name="access-your-security-data"></a>存取您的安全資料

IoT 的 Azure 安全中心在日誌分析工作區中存儲安全警報、建議和原始安全數據(如果選擇保存)。

## <a name="log-analytics"></a>Log Analytics

要設定使用紀錄分析工作區:

1. 開啟 IoT 中樞。
1. 點選 **「安全**」部分下的 **「概述**」邊欄
1. 按下 **「設定」** 並更改日誌分析工作區配置。

要在設定後存取紀錄分析工作區中的警報和建議,請執行以下操作:

1. 在 IoT 的 Azure 安全中心中選擇警報或建議。
1. 點選**進一步調查**,然後按下「**檢視哪些裝置具有此警報」,請按下此處並查看 DeviceId 列**。

有關從紀錄分析查詢資料的詳細資訊,請參閱[在紀錄分析中開始查詢](https://docs.microsoft.com//azure/log-analytics/query-language/get-started-queries)。

## <a name="security-alerts"></a>安全性警示

安全警報儲存在為 IoT 解決方案的 Azure 安全中心配置的日誌分析工作區中的_Azure 安全安全警報_表中。

我們提供了許多有用的查詢,以説明您開始探索安全警報。

### <a name="sample-records"></a>範例記錄

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
| 2018-11-18T18:10:29.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 野蠻武力攻擊成功           | 對裝置的暴力攻擊成功        |    •\""完整源位址":"10.165.12.18:"\"\"使用者名":"\","\"\"設備 Id":"IoT-Device-Linux" |                                                                       |
| 2018-11-19T12:40:31.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上成功本地登錄      | 偵測到裝置成功本地登錄     | • "遠端位址":"?","遠端埠":""、"本地埠":""、"登錄外殼":"/bin/su","登錄進程 ID":"28207","使用者名":"攻擊者"、"設備 Id":"IoT-Device-Linux" | |
| 2018-11-19T12:40:31.000 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上的本地登入連線失敗  | 偵測到裝置的本地登入失敗 |    • "遠端位址":"?","遠端埠":""、"本地埠":""、"登錄外殼":"/bin/su","登錄進程 ID":"22644"、"使用者名":"攻擊者"、"設備 Id":"IoT-Device-Linux" | |

### <a name="device-summary"></a>裝置摘要

獲取上周檢測到的不同安全警報數,按IoT中心、設備、警報嚴重性、警報類型分組。

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
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 裝置上的本地登入連線失敗  | 242 |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 高          | 裝置上成功本地登錄      | 31  |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中        | 加密硬幣礦工                     | 4   |

### <a name="iot-hub-summary"></a>IoT 中心摘要

通過 IoT 中心、警報嚴重性、警報類型選擇上周警報的多個不同設備

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
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 中        | 裝置上的本地登入連線失敗  | 1          |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 高          | 裝置上成功本地登錄      | 1          |
| /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | 中        | 加密硬幣礦工                     | 1          |

## <a name="security-recommendations"></a>安全性建議

安全建議存儲在為IoT解決方案的Azure安全中心配置的日誌分析工作區中的_Azure安全資訊.安全建議_表中。

我們提供了許多有用的查詢,以説明您開始探索安全建議。

### <a name="sample-records"></a>範例記錄

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

| TimeGenerated | IoTHubId | deviceId | 建議嚴重性 | 建議國 | 建議顯示名稱 | 描述 | 建議附加資料 |
|---------------|----------|----------|------------------------|---------------------|---------------------------|-------------|------------------------------|
| 2019-03-22T10:21:06.060 |    /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中 | Active | 在輸入鍊中找到讓防火牆規則 | 在防火牆中已發現一條規則,其中包含各種 IP 位址或埠的允許模式 | {"規則\"":"]源\"位址\"\"\"\":\"\"、\"\"\"\"源\"埠\"\":\"、 目的地 位址 : 、 目的地連接埠 : 1337 []] |
| 2019-03-22T10:50:27.237 | /訂閱/<subscription_id>/資源組/<resource_group>/供應商/微軟.設備/IotHubs/<iot_hub> | <device_name> | 中 | Active | 在輸入鍊中找到讓防火牆規則 | 在防火牆中已發現一條規則,其中包含各種 IP 位址或埠的允許模式 | {"規則\"":"]源\"位址\"\"\"\":\"\"、\"\"\"\"源\"埠\"\":\"、 目的地 位址 : 、 目的地連接埠 : 1337 []] |

### <a name="device-summary"></a>裝置摘要

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

- 閱讀 Azure 安全中心,瞭解 IoT[概述](overview.md)
- 瞭解適用於 IoT[架構結構](architecture.md)的 Azure 安全中心
- 瞭解並探索[適用於 IoT 警報的 Azure 安全中心](concept-security-alerts.md)
- 瞭解並探索[Azure 安全中心,瞭解 IoT 建議](concept-recommendations.md)
