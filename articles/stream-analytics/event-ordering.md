---
title: 設定 Azure 串流分析的事件順序原則
description: 本文說明如何在串流分析中設定甚至訂購設定
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: f7ec6f32b48a93a29210311c7ba6747eb2e2d066
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014290"
---
# <a name="configuring-event-ordering-policies-for-azure-stream-analytics"></a>設定 Azure 串流分析的事件順序原則

本文說明如何設定和使用 Azure 串流分析中的延遲抵達和不按照順序的事件原則。 只有當您在查詢中使用 [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) 子句時，才會套用這些原則，而且只適用于雲端輸入來源。

## <a name="event-time-and-arrival-time"></a>事件時間和抵達時間

您的串流分析作業可以根據 *事件時間* 或 *抵達時間* 處理事件。 **事件/應用程式時間** 是事件) 產生事件時，事件裝載 (中的時間戳記。 **抵達時間** 是在輸入來源收到事件 (事件中樞/IoT 中樞/Blob 儲存體) 的時間戳記。 

根據預設，串流分析會依 *抵達時間* 處理事件，但您可以選擇在查詢中使用 [TIMESTAMP by](/stream-analytics-query/timestamp-by-azure-stream-analytics)子句，依 *事件時間* 處理事件。 只有當您依事件時間處理事件時，才會出現延遲抵達和不按照順序的原則。 進行這些設定時，請考慮您案例的延遲性和正確性需求。 

## <a name="what-is-late-arrival-policy"></a>什麼是延遲傳入原則？

有時事件會因為各種原因而延遲抵達。 例如，到達40秒延遲的事件將會有事件時間 = 00:10:00，而抵達時間 = 00:10:40。 如果您將延遲抵達原則設定為15秒，則會捨棄超過15秒的任何事件， (不是由串流分析) 處理，或已調整其事件時間。 在上述範例中，當事件抵達40秒的延遲 (超過原則設定) ，其事件時間將會調整為延遲抵達原則 00:10:25 (抵達時間上限值) 的最大延遲抵達原則值。 預設的延遲抵達原則為5秒。

## <a name="what-is-out-of-order-policy"></a>什麼是非順序原則？ 

事件也可能不會按順序抵達。 當事件時間根據延遲抵達原則進行調整之後，您也可以選擇自動卸載或調整已不按照順序的事件。 如果您將此原則設定為8秒，則任何未依順序抵達但在8秒時間範圍內的事件，都會依事件時間重新排序。 稍後抵達的事件將會捨棄或調整為最大順序原則值。 預設的順序外原則為0秒。 

## <a name="adjust-or-drop-events"></a>調整或捨棄事件

如果事件根據您所設定的原則，以延遲或不按順序抵達，您可以卸載這類事件， (不是由串流分析) 處理，或已調整其事件時間。

讓我們看看這些原則的運作方式範例。
<br> **延遲抵達原則：** 15 秒
<br> **順序外原則：** 8 秒

| 事件編號 | 事件時間 | 抵達時間 | System.Timestamp | 說明 |
| --- | --- | --- | --- | --- |
| **1** | 00:10:00  | 00:10:40  | 00:10:25  | 事件到達延遲和外部容錯層級。 因此，事件時間會調整為最大延遲抵達容錯。  |
| **2** | 00:10:30 | 00:10:41  | 00:10:30  | 事件延誤抵達，但在容錯層級內。 所以不會調整事件時間。  |
| **3** | 00:10:42 | 00:10:42 | 00:10:42 | 事件準時抵達。 不需要進行調整。  |
| **4** | 00:10:38  | 00:10:43  | 00:10:38 | 事件未依順序抵達，但在8秒的容錯範圍內。 因此，無法調整事件時間。 基於分析用途，此事件將視為先前的事件編號4。  |
| **5** | 00:10:35 | 00:10:45  | 00:10:37 | 事件以非順序抵達，以及8秒的外部容錯。 因此，會將事件時間調整為最大的順序容錯。 |

## <a name="can-these-settings-delay-output-of-my-job"></a>這些設定是否會延遲作業的輸出？ 

可以。 依預設，依序將原則設定為零 (00 分鐘和00秒) 。 如果您變更預設值，則作業的第一個輸出會以這個值延遲 (或更大的) 。 

如果您輸入的其中一個資料分割未收到事件，您應該預期您的輸出會被延遲抵達原則值所延遲。 若要瞭解原因，請閱讀下面的 InputPartition 錯誤一節。 

## <a name="i-see-lateinputevents-messages-in-my-activity-log"></a>我在活動記錄中看到 LateInputEvents 訊息

系統會顯示這些訊息，以通知您事件已延遲抵達，並且根據您的設定而捨棄或調整。 如果您已適當地設定延遲抵達原則，您可以忽略這些訊息。 

此訊息的範例為： <br>
<code>
{"message Time":"2019-02-04 17:11:52Z","error":null,
"message":"First Occurred: 02/04/2019 17:11:48 | Resource Name: ASAjob | Message: Source 'ASAjob' had 24 data errors of kind 'LateInputEvent' between processing times '2019-02-04T17:10:49.7250696Z' and '2019-02-04T17:11:48.7563961Z'. Input event with application timestamp '2019-02-04T17:05:51.6050000' and arrival time '2019-02-04T17:10:44.3090000' was sent later than configured tolerance.","type":"DiagnosticMessage","correlation ID":"49efa148-4asd-4fe0-869d-a40ba4d7ef3b"} 
</code>

## <a name="i-see-inputpartitionnotprogressing-in-my-activity-log"></a>我在活動記錄中看到 InputPartitionNotProgressing

您 (事件中樞/IoT 中樞) 的輸入來源可能有多個磁碟分割。 只有在合併的所有資料分割至少為時間 t1 時，Azure 串流分析才會產生時間戳記 t1 的輸出。 例如，假設查詢是從具有兩個分割區的事件中樞分割區讀取。 其中一個分割區 P1 直到時間 t1 之前具有事件。 另一個分割區 P2 直到時間 t1 + x 之前具有事件。 然後到時間 t1 會產生輸出。 但是如果有明確的 Partition by PartitionId 子句，則兩個分割區會單獨進行。 

當結合相同輸入資料流程的多個資料分割時，延遲抵達容錯是每個分割區等候新資料的最大時間量。 如果事件中樞內有一個資料分割，或如果 IoT 中樞未收到輸入，則該分割區的時間軸將不會進行，直到達到延遲抵達容錯閾值為止。 這會延遲延遲抵達容錯閾值的輸出。 在這種情況下，您可能會看到下列訊息：
<br><code>
{"message Time":"2/3/2019 8:54:16 PM UTC","message":"Input Partition [2] does not have additional data for more than [5] minute(s). Partition will not progress until either events arrive or late arrival threshold is met.","type":"InputPartitionNotProgressing","correlation ID":"2328d411-52c7-4100-ba01-1e860c757fc2"} 
</code><br><br>
這則訊息會通知您，您的輸入中至少有一個資料分割是空的，而且會延遲延遲抵達閾值來延遲您的輸出。 若要解決此情況，建議您執行下列其中一項：  
1. 確定事件中樞/IoT 中樞的所有資料分割都會接收輸入。 
2. 在您的查詢中使用 Partition by PartitionID 子句。 

## <a name="why-do-i-see-a-delay-of-5-seconds-even-when-my-late-arrival-policy-is-set-to-0"></a>當我的延遲抵達原則設定為0時，為什麼會看到5秒的延遲？
當輸入資料分割未曾收到任何輸入時，就會發生這種情況。 您可以依資料分割確認輸入度量，以驗證此行為。 

當資料分割沒有超過設定延遲抵達閾值的任何資料時，串流分析會將應用程式時間戳記前移，如事件順序考慮一節中所述。 這需要預估的抵達時間。 如果資料分割永遠沒有任何資料，則串流分析會以 *當地時間（5秒）* 預估抵達時間。 由於此資料分割永遠沒有任何資料，因此可能會顯示5秒的浮水印延遲。  

## <a name="next-steps"></a>後續步驟
* [時間掌握考量](stream-analytics-time-handling.md)
* [串流分析中的可用計量](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)