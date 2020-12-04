---
title: Azure 事件方格-主題或網域的診斷記錄
description: 本文提供有關 Azure 事件方格主題或網域的診斷記錄的概念資訊。
ms.topic: conceptual
ms.date: 12/03/2020
ms.openlocfilehash: 36ade14932b5d25c7a1fe05632da671de68ba3bb
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574981"
---
#  <a name="diagnostic-logs-for-azure-event-grid-topicsdomains"></a>適用于 Azure 事件方格主題/網域的診斷記錄
診斷設定可讓事件方格使用者在儲存體帳戶、事件中樞或 Log Analytics 工作區中，捕獲和查看 **發佈和傳遞失敗** 記錄。 本文提供記錄的架構和範例記錄專案。


## <a name="schema-for-publishdelivery-failure-logs"></a>發行/傳遞失敗記錄的架構

| 屬性名稱 | 資料類型 | 描述 |
| ------------- | --------- | ----------- | 
| Time | Datetime | 產生記錄專案的時間 <p>**範例值：**  01-29-2020 09：52：02.700</p> |
| EventSubscriptionName | String | 事件訂用帳戶的名稱 <p>**範例值：** "EVENTSUB1"</p> <p>這個屬性只存在於傳遞失敗記錄中。</p>  |
| 類別 | String | 記錄類別目錄名稱。 <p>**範例值：** "DeliveryFailures" 或 "PublishFailures" | 
| OperationName | String | 造成失敗的作業名稱。<p>**範例值：** 傳遞失敗的「傳遞」。 |
| 訊息 | String | 說明失敗原因的使用者記錄訊息，以及其他詳細資料。 |
| ResourceId | String | 主題/網域資源的資源識別碼<p>**範例值：**`/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example"></a>範例

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

## <a name="next-steps"></a>後續步驟
若要瞭解如何啟用主題或網域的診斷記錄，請參閱 [啟用診斷記錄](enable-diagnostic-logs-topic.md)。
