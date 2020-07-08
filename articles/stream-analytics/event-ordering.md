---
title: 設定 Azure 串流分析的事件順序原則
description: 本文說明如何在串流分析中設定甚至訂購設定。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/12/2019
ms.openlocfilehash: e9617018b06d4f62b49946ae5593bd51805355e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86044561"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>設定 Azure 串流分析的事件順序原則

本文說明如何在 Azure 串流分析中設定和使用延遲抵達和順序不限的事件原則。 只有當您在查詢中使用[TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句，而且只適用于雲端輸入來源時，才會套用這些原則。

## <a name="event-time-and-arrival-time"></a>事件時間和抵達時間

您的串流分析作業可以根據*事件時間*或*抵達時間*來處理事件。 **事件/應用程式時間**是事件裝載中的時間戳記（產生事件時）。 **抵達時間**是在輸入來源（事件中樞/IoT 中樞/Blob 儲存體）收到事件時的時間戳記。 

根據預設，串流分析會依*抵達時間*處理事件，但您可以選擇在查詢中使用[TIMESTAMP by](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)子句，依*事件時間*來處理事件。 延遲抵達和順序外的原則只有在您依事件時間處理事件時才適用。 進行這些設定時，請考慮您案例的延遲性和正確性需求。 

## <a name="what-is-late-arrival-policy"></a>什麼是延遲傳入原則？

有時候事件會因為各種原因而延遲抵達。 例如，延遲40秒的事件會有事件時間 = 00:10:00，而抵達時間 = 00:10:40。 如果您將延遲抵達原則設定為15秒，則超過15秒的任何事件都會被捨棄（而不是由串流分析處理）或其事件時間調整。 在上述範例中，當事件抵達40秒（超過原則設定）時，其事件時間將會調整為最大延遲抵達原則00:10:25 （抵達時間-延遲抵達原則值）。 預設的延遲抵達原則為5秒。

## <a name="what-is-out-of-order-policy"></a>什麼是不按照順序的原則？ 

事件可能也不會按順序抵達。 根據延遲抵達原則調整事件時間之後，您也可以選擇自動卸載或調整順序不好的事件。 如果您將此原則設定為8秒，則會依事件時間重新排列以不按照順序抵達，但在8秒範圍內的任何事件。 稍後抵達的事件將會被捨棄或調整為 [最大順序] 原則值。 預設的非順序原則為0秒。 

## <a name="adjust-or-drop-events"></a>調整或捨棄事件

如果事件是根據您已設定的原則，以延遲或不按照順序抵達，您可以卸載這類事件（不是由串流分析處理），或將其事件時間調整。

讓我們查看這些原則的運作範例。
<br> **延遲抵達原則：** 15 秒
<br> 不**按照順序的原則：** 8 秒

| 事件編號 | 事件時間 | 抵達時間 | System.Timestamp | 說明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件在容錯層級的延遲和外部抵達。 因此，事件時間會調整為最大延遲抵達容錯。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件延遲到達，但在容錯層級內。 所以不會調整事件時間。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件準時抵達。 不需要調整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件未按順序抵達，但在8秒的容錯範圍內。 因此，不會調整事件時間。 基於分析目的，此事件將會視為先前的事件編號4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件抵達順序和外部容錯（8秒）。 因此，事件時間會調整為最大的容錯順序。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>這些設定是否可以延遲我的作業輸出？ 

是。 根據預設，依順序排列的原則會設定為零（00分和00秒）。 如果您變更預設值，則作業的第一個輸出會延遲此值（或更高）。 

如果您輸入的其中一個資料分割不會收到事件，您應該預期輸出會延遲抵達原則的值。 若要瞭解原因，請閱讀下面的 InputPartition 錯誤一節。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在活動記錄中看到 LateInputEvents 的訊息

系統會顯示這些訊息，通知您事件已延遲抵達，並根據您的設定加以捨棄或調整。 如果您已適當設定延遲抵達原則，可以忽略這些訊息。 

此訊息的範例如下： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在活動記錄中看到 InputPartitionNotProgressing

您的輸入來源（事件中樞/IoT 中樞）可能會有多個磁碟分割。 只有在所有合併的分割區至少在時間 t1 時，Azure 串流分析才會產生時間戳記 t1 的輸出。 例如，假設查詢是從具有兩個分割區的事件中樞分割區讀取。 其中一個分割區 P1 直到時間 t1 之前具有事件。 另一個分割區 P2 直到時間 t1 + x 之前具有事件。 然後到時間 t1 會產生輸出。 但是如果有明確的 Partition by PartitionId 子句，則兩個分割區會單獨進行。 

結合來自相同輸入資料流程的多個資料分割時，延遲傳入容錯是每個分割區等候新資料的最大時間量。 如果事件中樞內有一個資料分割，或者 IoT 中樞不會收到輸入，則該資料分割的時間軸將不會進行，直到達到延遲抵達容錯閾值為止。 這會延遲抵達容錯閾值的輸出。 在這種情況下，您可能會看到下列訊息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
這則訊息會通知您，輸入中至少有一個資料分割是空的，而且會延遲抵達閾值來延遲輸出。 若要解決此情況，建議您執行下列其中一項：  
1. 請確定事件中樞/IoT 中樞的所有分割區都會接收輸入。 
2. 在您的查詢中使用 Partition by PartitionID 子句。 

## <a name="next-steps"></a>後續步驟
* [時間掌握考量](stream-analytics-time-handling.md)
* [串流分析中的可用計量](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-monitoring#metrics-available-for-stream-analytics)
