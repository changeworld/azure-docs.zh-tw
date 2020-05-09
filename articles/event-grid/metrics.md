---
title: Azure 事件方格支援的計量
description: 本文提供 Azure 事件方格服務所支援的 Azure 監視器計量。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630131"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure 事件方格支援的計量
本文提供以命名空間分類的事件方格計量清單。 

## <a name="microsofteventgriddomains"></a>EventGrid/網域

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|主題|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|主題、ErrorType、錯誤|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType，錯誤|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|MatchedEventCount|相符的事件|計數|總計|與此事件訂閱相符的事件總數|無|
|DeliveryAttemptFailCount|傳遞失敗的事件|計數|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType|
|DeliverySuccessCount|已傳遞的事件|計數|總計|已傳遞到此事件訂閱的事件總數|無|
|DestinationProcessingDurationInMs|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|無|
|DroppedEventCount|卸除的事件|計數|總計|與此事件訂閱相符的已卸除事件總數|DropReason|
|DeadLetteredCount|失效信件事件|計數|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|
|PublishSuccessCount|已發佈的事件|計數|總計|發佈至本主題的事件總數|無|
|PublishFailCount|發行失敗的事件|計數|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|UnmatchedEventCount|不相符的事件|計數|總計|不符合本主題中任何事件訂閱的事件總數|無|
|PublishSuccessLatencyInMs|發佈成功延遲|毫秒|總計|發佈成功延遲（以毫秒為單位）|無|

## <a name="next-steps"></a>後續步驟
請參閱下列文章：[診斷記錄](diagnostic-logs.md)
