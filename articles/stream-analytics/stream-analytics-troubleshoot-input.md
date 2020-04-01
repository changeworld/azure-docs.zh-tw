---
title: 針對 Azure 串流分析的輸入進行疑難排解
description: 本文說明針對 Azure 串流分析作業中的輸入連線進行疑難排解的技術。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409404"
---
# <a name="troubleshoot-input-connections"></a>針對輸入連線進行疑難排解

本文介紹了 Azure 流分析輸入連接的常見問題、如何排除輸入問題以及如何更正問題。 許多故障排除步驟都需要為流分析作業啟用診斷日誌。 如果未啟用診斷紀錄,請參閱[使用診斷紀錄對 Azure 串流分析進行故障排除](stream-analytics-job-diagnostic-logs.md)。

## <a name="input-events-not-received-by-job"></a>作業未收到輸入事件 

1.  測試輸入和輸出連接。 針對各個輸入和輸出使用 [測試連線]**** 按鈕，驗證輸入及輸出的連線能力。

2.  檢查您的輸入資料。

    1. 對每個輸入使用[**「示例數據**](stream-analytics-sample-data-input.md)」按鈕。 下載輸入範例數據。
        
    1. 檢查範例資料以瞭解架構與[資料型態](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)。
    
    1. 檢查[事件中心指標](../event-hubs/event-hubs-metrics-azure-monitor.md)以確保發送事件。 如果事件中心正在接收消息,則消息指標應大於零。

3.  確保在輸入預覽中選擇時間範圍。 選擇 **「選擇時間範圍**」,然後在測試查詢之前輸入示例持續時間。

## <a name="malformed-input-events-causes-deserialization-errors"></a>格式不正確的輸入事件導致還原序列化錯誤 

當串流分析作業的輸入資料流包含格式不正確的訊息時，就會導致還原序列化問題。 例如,在 JSON 物件中缺少括弧或大括弧或時間欄位中的時間戳格式不正確,可能導致格式錯誤的消息。 
 
當串流分析作業從輸入收到格式不正確的訊息時，會捨棄訊息並以警告通知您。 您串流分析作業的 [輸入]**** 圖格上會顯示警告符號。 只要作業處於運行狀態,以下警告符號就存在:

![Azure 串流分析輸入圖格](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

啟用診斷日誌以查看導致錯誤的錯誤的詳細資訊以及導致錯誤的消息(有效負載)。 發生反序列化錯誤的原因有多種。 有關特定反序列化錯誤的詳細資訊,請參閱[輸入資料錯誤](data-errors.md#input-data-errors)。 如果未啟用診斷日誌,則 Azure 門戶中將提供簡要通知。

![輸入詳細資訊警告通知](media/stream-analytics-malformed-events/warning-message-with-offset.png)

如果消息負載大於 32 KB 或採用二進位格式,請運行[GitHub 範例儲存庫](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)中可用的 CheckMalformedEvents.cs 代碼。 此程式碼讀取分割區識別碼、位移，並列印位於該位移的資料。 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>工作超過最大事件中心接收器

使用事件中心的最佳做法是使用多個消費者組來進行作業可伸縮性。 在特定輸入的串流分析作業中，其讀取器數量會影響單一取用者群組中的讀取器數量。 接收器精確數量會以相應放大拓撲邏輯的內部實作詳細資料為基礎，並且不會對外公開。 讀取器數量會在作業開始時或作業升級期間變更。

當接收器數目超過上限時顯示的錯誤訊息為：

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> 如果在作業升級期間變更讀取器數量，暫時性警告會寫入稽核記錄中。 串流分析作業會從這些暫時性問題中自動復原。

### <a name="add-a-consumer-group-in-event-hubs"></a>在事件中樞新增取用者群組

若要在事件中樞執行個體中新增新的取用者群組，請遵循下列步驟：

1. 登入 Azure 入口網站。

2. 找到您的活動中心。

3. 選取 [實體]**** 標題下方的 [事件中樞]****。

4. 依名稱選取事件中樞。

5. 在 [事件中樞執行個體]**** 頁面上，[實體]**** 標題下方，選取 [取用者群組]****。 隨即列出名為 **$Default** 的取用者群組。

6. 選取 [+ 取用者群組]**** 以新增取用者群組。 

   ![在事件中樞新增取用者群組](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 當您在串流分析作業中建立輸入以指向事件中樞時，您已指定取用者群組。 當未指定時,將使用 **$Default。** 一旦您建立新的取用者群組後，請在串流分析作業中編輯事件中樞輸入，並指定新取用者群組的名稱。

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>每個分割區的讀取器超過事件中樞上限

如果串流查詢語法參考相同的輸入事件中樞資源多次，則作業引擎會為相同取用者群組中的每個查詢使用多個讀取器。 當對同一個取用者群組有過多參考時，作業會超過五個的限制，並且擲回錯誤。 在這些狀況下，您可以使用下節中所述的解決方案，在多個取用者群組之間使用多個輸入，藉此進一步地分割。 

讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況如下：

* 多個 SELECT 陳述式︰ 如果您使用多個 SELECT 陳述式參照**相同**的事件中樞輸入，則每個 SELECT 陳述式皆會造成新的接收器建立。

* UNION︰當您使用 UNION 時，就有可能會有多個輸入參照**相同**的事件中樞和取用者群組。

* SELF JOIN︰當您使用 SELF JOIN 作業時，就可能發生參照**相同**事件中樞多次的情形。

以下最佳做法可減少讀取器數量 (每個分割區) 超過事件中樞上限 (5 個) 的情況。

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>使用 WITH 子句將查詢分割成多個步驟

WITH 子句會指定名為結果集的暫存，並且可由查詢中的 FROM 子句加以參照。 您可以定義單一 SELECT 陳述式執行範圍的 WITH 子句。

例如，不要使用此查詢︰

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

使用此查詢︰

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>請確定這些輸入會繫結至不同的取用者群組

如果查詢中有三個以上的輸入與同一個事件中樞取用者群組連線，請建立個別的取用者群組。 這需要建立其他串流分析輸入。

## <a name="get-help"></a>取得說明

有關進一步説明,請嘗試我們的[Azure 流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 串流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
