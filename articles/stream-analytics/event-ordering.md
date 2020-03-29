---
title: 配置 Azure 流分析的事件排序策略
description: 本文介紹如何在流分析中配置甚至排序設置
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c0a108565a6a0f62c6252113f984e8b10967c5db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461185"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>配置 Azure 流分析的事件排序策略

本文介紹如何在 Azure 流分析中設置和使用延遲到達和順序外的事件策略。 僅當在查詢中使用[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句時，才會應用這些策略。

## <a name="event-time-and-arrival-time"></a>啟用時間和到達時間

您的流分析作業可以根據*事件時間*或*到達時間*處理事件。 **事件/應用程式時間是**事件裝載中存在的時間戳記（建置事件時）。 **到達時間是**在輸入源（事件中心/IoT 中心/Blob 存儲）接收事件時的時間戳記。 

預設情況分析按*到達時間*處理事件，但您可以選擇在查詢中使用[TIMESTAMP BY](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句按*事件時間*處理事件。 僅當按事件時間處理事件時，才適用延遲到達和訂單外策略。 進行這些設定時，請考慮您案例的延遲性和正確性需求。 

## <a name="what-is-late-arrival-policy"></a>什麼是延遲傳入原則？

有時由於各種原因，事件遲到。 例如，遲到 40 秒的事件的事件事件時間 = 00：10：00，到達時間 = 00：10：40。 如果將延遲到達策略設置為 15 秒，則任何晚到 15 秒的事件都將被丟棄（不由流分析處理），或者調整其事件時間。 在上面的示例中，當事件延遲 40 秒到達（超過策略集）時，其事件時間將調整為延遲策略的最大值 00：10：25（到達時間 - 延遲到達策略值）。 預設延遲到達策略為 5 秒。

## <a name="what-is-out-of-order-policy"></a>什麼是訂單外策略？ 

活動也可能不有序地到達。 根據延遲到達政策調整事件時間後，您還可以選擇自動刪除或調整順序外的事件。 如果將此策略設置為 8 秒，則無序但 8 秒視窗內的任何事件都按事件時間重新排序。 稍後到達的事件將被丟棄或調整為最大訂單外策略值。 預設的訂單外策略為 0 秒。 

## <a name="adjust-or-drop-events"></a>調整或刪除事件

如果事件到達延遲或順序不正確，具體取決於您配置的策略，則可以刪除此類事件（未由流分析處理）或調整其事件時間。

讓我們看到這些政策在起作用的例子。
<br> **延遲到達政策：15**秒
<br> 訂單**外策略：8**秒

| 事件號 | 事件時間 | 抵達時間 | System.Timestamp | 說明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件到達較晚和外部容差水準。 因此，事件時間會調整為最大延遲到達容差。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件到達較晚，但在容差級別內。 因此，事件時間不會調整。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 活動準時到達。 無需調整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件到達順序，但在 8 秒的容差內。 因此，事件時間不會調整。 出於分析目的，此事件將被視為前面的事件編號 4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件到達順序和外部公差 8 秒。 因此，事件時間會調整為訂單外容差的最大。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>這些設置會延遲我的工作輸出嗎？ 

是。 預設情況下，訂單外策略設置為零（00 分鐘 00 秒）。 如果更改預設值，作業的第一個輸出將延遲此值（或更大）。 

如果輸入的一個分區未接收事件，則應預計輸出會因延遲到達策略值而延遲。 要瞭解原因，請閱讀下面的"輸入分區錯誤"部分。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在活動日誌中看到"延遲輸入事件"消息

顯示這些消息是為了通知您事件來得較晚，並且會根據您的配置被丟棄或調整。 如果已正確配置延遲到達策略，則可以忽略這些消息。 

此消息的示例包括： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在活動日誌中看到輸入分區未前進

您的輸入源（事件中心/IoT 中心）可能有多個分區。 Azure 流分析僅在合併的所有分區至少為時間 t1 後，才會生成時間戳記 t1 的輸出。 例如，假設查詢是從具有兩個分割區的事件中樞分割區讀取。 其中一個分割區 P1 直到時間 t1 之前具有事件。 另一個分割區 P2 直到時間 t1 + x 之前具有事件。 然後到時間 t1 會產生輸出。 但是如果有明確的 Partition by PartitionId 子句，則兩個分割區會單獨進行。 

當合併來自同一輸入流的多個分區時，延遲到達容差是每個分區等待新資料的最大時間量。 如果事件中心中有一個分區，或者 IoT 中心未接收輸入，則該分區的時間表在到達延遲到達容差閾值之前不會進行。 這將延遲輸出，因為延遲到達公差閾值。 在這種情況下，您可能會看到以下消息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
此消息通知您輸入中至少有一個分區為空，並且會延遲輸出到晚到達閾值。 為了克服這一點，建議您：  
1. 確保事件中心/IoT 中心的所有分區都接收輸入。 
2. 在查詢中使用分區分區 ID 子句。 

## <a name="next-steps"></a>後續步驟
* [時間掌握考量](stream-analytics-time-handling.md)
* [串流分析中的可用計量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
