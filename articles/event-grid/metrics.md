---
title: Azure 事件方格支援的計量
description: 本文提供 Azure 事件方格服務所支援的 Azure 監視器計量。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 06c43b1990efc977cae33ced3f66f02e2de0b9c4
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225167"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure 事件方格支援的計量
本文提供以命名空間分類的事件方格計量清單。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|主題、ErrorType、錯誤|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|主題|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|無維度|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|無維度|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|無維度|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|是|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|否|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|是|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|否|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|是|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|是|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|是|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|是|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|是|發行成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無維度|
|UnmatchedEventCount|是|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|

## <a name="next-steps"></a>後續步驟
請參閱下列文章： [診斷記錄](diagnostic-logs.md)
