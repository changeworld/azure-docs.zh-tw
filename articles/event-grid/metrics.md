---
title: Azure 事件方格支援的計量
description: 本文提供 Azure 事件方格服務所支援的 Azure 監視器計量。
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042138"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure 事件方格支援的計量
本文提供依命名空間分類的事件方格計量清單。 

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domains

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DeadLetterReason|
|DeliveryAttemptFailCount|No|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、Error、ErrorType|
|DeliverySuccessCount|Yes|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|主題、EventSubscriptionName、DomainEventSubscriptionName|
|DroppedEventCount|Yes|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName、DropReason|
|MatchedEventCount|Yes|相符的事件|Count|總計|與此事件訂閱相符的事件總數|主題、EventSubscriptionName、DomainEventSubscriptionName|
|PublishFailCount|Yes|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|主題、ErrorType、錯誤|
|PublishSuccessCount|Yes|已發佈的事件|Count|總計|發佈至本主題的事件總數|主題|
|PublishSuccessLatencyInMs|Yes|發行成功延遲|毫秒|總計|發佈成功延遲（毫秒）|無維度|
| AdvancedFilterEvaluationCount | Yes | Advanced Filter 評估 | 計數 | 總計 | 跨事件訂閱評估的 advanced 濾波器總計 | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason|
|DeliveryAttemptFailCount|No|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType|
|DeliverySuccessCount|Yes|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|無維度|
|DestinationProcessingDurationInMs|No|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|無維度|
|DroppedEventCount|Yes|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason|
|MatchedEventCount|Yes|相符的事件|Count|總計|與此事件訂閱相符的事件總數|無維度|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|Yes|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|Yes|發行成功延遲|毫秒|總計|發佈成功延遲（毫秒）|無維度|
|UnmatchedEventCount|Yes|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|


## <a name="microsofteventgridsystemtopics"></a>EventGrid/systemTopics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|Yes|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|Yes|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|Yes|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|Yes|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|Yes|發行成功延遲|毫秒|總計|發佈成功延遲（毫秒）|無維度|
|UnmatchedEventCount|Yes|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|
| AdvancedFilterEvaluationCount | Yes | Advanced Filter 評估 | 計數 | 總計 | 跨事件訂閱評估的 advanced 濾波器總計 | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|計量|是否可透過診斷設定匯出？|計量顯示名稱|單位|彙總類型|描述|維度|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|失效信件事件|Count|總計|與此事件訂閱相符的失效信件事件總數|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|傳遞失敗的事件|Count|總計|傳遞到此事件訂閱失敗的事件總數|Error、ErrorType、EventSubscriptionName|
|DeliverySuccessCount|Yes|已傳遞的事件|Count|總計|已傳遞到此事件訂閱的事件總數|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|目的端處理持續期間|毫秒|Average|目的端處理持續期間，以毫秒為單位|EventSubscriptionName|
|DroppedEventCount|Yes|卸除的事件|Count|總計|與此事件訂閱相符的已卸除事件總數|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|相符的事件|Count|總計|與此事件訂閱相符的事件總數|EventSubscriptionName|
|PublishFailCount|Yes|發行失敗的事件|Count|總計|發佈至本主題失敗的事件總數|ErrorType、Error|
|PublishSuccessCount|Yes|已發佈的事件|Count|總計|發佈至本主題的事件總數|無維度|
|PublishSuccessLatencyInMs|Yes|發行成功延遲|毫秒|總計|發佈成功延遲（毫秒）|無維度|
|UnmatchedEventCount|Yes|不相符的事件|Count|總計|不符合本主題中任何事件訂閱的事件總數|無維度|
| AdvancedFilterEvaluationCount | Yes | Advanced Filter 評估 | 計數 | 總計 | 跨事件訂閱評估的 advanced 濾波器總計 | 主題、EventSubscriptionName、DomainEventSubscriptionName |

## <a name="next-steps"></a>後續步驟
請參閱下列文章： [診斷記錄](diagnostic-logs.md)
