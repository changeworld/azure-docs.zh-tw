---
title: 監視 Azure 時間序列深入解析資料參考 |Microsoft Docs
description: 監視 Azure 時間序列深入解析的參考檔。
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: 0b564ddfdea2cf24b7f9b1bc608d47fa4cfe541b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632604"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>監視 Azure 時間序列深入解析資料參考

瞭解 Azure 監視器從 Azure 時間序列深入解析環境收集的資料和資源。 如需收集和分析監視資料的詳細資訊，請參閱 [監視時間序列深入]( ./how-to-monitor-tsi.md) 解析。

## <a name="metrics"></a>計量

此區段會列出針對 Azure 時間序列深入解析收集的所有自動收集平臺計量。 如需所有 Azure 監視器支援計量的清單 (包含 Azure 時間序列深入解析) ，請參閱 [Azure 監視器支援的度量](../azure-monitor/platform/metrics-supported.md)。 這些計量的資源提供者是 [>201-timeseriesinsights-environment-with-eventhub/環境/eventsources](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) 和 [>201-timeseriesinsights-environment-with-eventhub/環境](../azure-monitor/platform/metrics-supported.md#microsofttimeseriesinsightsenvironments)。


### <a name="ingress"></a>輸入
 
|計量|計量顯示名稱|單位|彙總類型|Description|
|---|---|---|---|---|
|IngressReceivedBytes|輸入接收的位元組|位元組|總計|從事件來源讀取的位元組計數|
|IngressReceivedInvalidMessages|輸入接收的無效訊息|計數|總計|從事件來源讀取的無效訊息計數|
|IngressReceivedMessages|輸入接收的訊息|計數|總計|從事件來源讀取的訊息計數|
|IngressReceivedMessagesCountLag|輸入接收的訊息計數延遲|Count|Average|事件來源資料分割中最後排入佇列之訊息的序號與輸入中所處理訊息的序號之間的差異|
|IngressReceivedMessagesTimeLag|輸入接收的訊息時間延遲|秒|最大值|訊息在事件來源中加入佇列的時間與在輸入中處理的時間之間的差異|
|IngressStoredBytes|輸入儲存的位元組|位元組|總計|已成功處理且可供查詢的事件總大小|
|IngressStoredEvents|輸入儲存的事件|計數|總計|已成功處理且可供查詢的壓平合併事件計數|

### <a name="storage"></a>儲存體

|計量|計量顯示名稱|單位|彙總類型|Description|
|---|---|---|---|---|
|WarmStorageMaxProperties|暖儲存體最大屬性|Count|最大值|適用于 S1/S2 SKU 的環境所允許的最大屬性數目，以及 PAYG SKU 的暖存放區所允許的最大屬性數目|
|WarmStorageUsedProperties|暖儲存體使用的屬性 |Count|最大值|適用于 S1/S2 SKU 的環境所使用的屬性數目，以及 PAYG SKU 的暖存放區所使用的屬性數目|

## <a name="resource-logs"></a>資源記錄

此區段會列出您可以為 Azure 時間序列深入解析環境收集的資源記錄類型。

| 類別 | 顯示名稱 | 描述 |
|----- |----- |----- |
| 輸入 | TSIIngress | 輸入類別會追蹤輸入管線中發生的錯誤。 這個類別包含接收事件時所發生的錯誤 (例如，連接到事件來源) 和處理事件時所發生的錯誤 (例如剖析事件承載) 時發生錯誤。 |

## <a name="schemas"></a>結構描述
下列架構正在使用中 Azure 時間序列深入解析

### <a name="tsiingress-table"></a>TSIIngress 資料表

| 屬性 | 描述 |
|----- |----- |
| TimeGenerated | 產生此事件的時間 (UTC) 。 |
| 位置 | 資源的位置。 |
| 類別 | 記錄檔事件的類別。 |
| OperationName | 事件的作業名稱。 |
| CorrelationId | 要求的相互關聯識別碼。 |
| 層級 | 事件的嚴重性層級。 |
| ResultDescription | 作業結果的描述，例如「已接收禁止的錯誤」。 |
| 訊息 | 與錯誤相關聯的訊息。 包含發生問題的詳細資料，以及如何緩和錯誤的詳細資料。 |
| ErrorCode | 與錯誤相關聯的程式碼 |
| EventSourceType | 事件來源的類型。 它可以是事件中樞或 IoT 中樞。 |
| EventSourceProperties | 特定于事件來源的屬性集合。 包含詳細資料，例如取用者群組和存取金鑰名稱。 |
