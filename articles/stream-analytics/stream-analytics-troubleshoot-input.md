---
title: 針對 Azure 串流分析的輸入進行疑難排解
description: 本文說明針對 Azure 串流分析作業中的輸入連線進行疑難排解的技術。
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: dac3037f82c38980c9ac16685aa7fddac68a2e7b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720294"
---
# <a name="troubleshoot-input-connections"></a>針對輸入連線進行疑難排解

本頁面說明輸入連線的常見問題，以及如何進行疑難排解。

## <a name="input-events-not-received-by-job"></a>作業未收到輸入事件 
1.  測試連線能力。 針對各個輸入和輸出使用 [測試連線] 按鈕，驗證輸入及輸出的連線能力。

2.  檢查您的輸入資料。

    1. 若要驗證輸入資料是否正流入事件中樞，請使用[服務匯流排總管](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a)與 Azure 事件中樞連線 (若有使用事件中樞輸入)。
        
    1. 針對每個輸入使用 [[**範例資料**](stream-analytics-sample-data-input.md)] 按鈕。 下載輸入範例資料。
        
    1. 檢查範例資料以瞭解資料的形式，也就是架構和[資料類型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)。

3.  請確定您已在輸入預覽中選取時間範圍。 選擇 [**選取時間範圍**]，然後在測試查詢之前輸入取樣持續時間。

## <a name="malformed-input-events-causes-deserialization-errors"></a>格式不正確的輸入事件導致還原序列化錯誤 
當串流分析作業的輸入資料流包含格式不正確的訊息時，就會導致還原序列化問題。 例如，格式錯誤的訊息可能是在 JSON 物件中遺漏括號或遺漏大括號所導致，或是時間欄位中不正確的時間戳記格式所導致。 
 
當串流分析作業從輸入收到格式不正確的訊息時，會捨棄訊息並以警告通知您。 您串流分析作業的 [輸入] 圖格上會顯示警告符號。 只要作業處於執行中狀態，此警告符號就會存在：

![Azure 串流分析輸入圖格](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

啟用診斷記錄以檢視警告的詳細資料。 對於格式錯誤的輸入事件，執行記錄包含具有類似以下訊息的項目： 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>造成還原序列化錯誤的原因
您可以採取下列步驟來詳細分析輸入事件，以便清楚了解造成還原序列化錯誤的原因。 接著您可以修正事件來源，使其產生正確格式的事件來防止再次遇到此問題。

1. 瀏覽至 [輸入] 圖格，然後按一下警告符號以查看問題的清單。

2. 輸入詳細資料圖格會顯示警告清單，以及每個問題的相關詳細資料。 以下的範例警告訊息包含發生 JSON 資料格式不正確的磁碟分割、位移及序號。 

   ![具位移的串流分析警告訊息](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. 若要尋找格式錯誤的 JSON 資料，請執行 CheckMalformedEvents.cs 程式碼，該程式碼可從 [GitHub 範例存放庫](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH)取得。 此程式碼讀取分割區識別碼、位移，並列印位於該位移的資料。 

4. 一旦您讀取資料後，可以分析並修正序列化格式。

5. 您也可以[透過 Service Fabric Explorer 從 IoT 中樞讀取事件](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b)。

## <a name="job-exceeds-maximum-event-hub-receivers"></a>作業超過事件中樞接收器最大值
使用事件中樞的最佳做法是使用多個取用者群組，以確保作業延展性。 在特定輸入的串流分析作業中，其讀取器數量會影響單一取用者群組中的讀取器數量。 接收器精確數量會以相應放大拓撲邏輯的內部實作詳細資料為基礎，並且不會對外公開。 讀取器數量會在作業開始時或作業升級期間變更。

當接收器數目超過上限時顯示的錯誤訊息為：`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> 如果在作業升級期間變更讀取器數量，暫時性警告會寫入稽核記錄中。 串流分析作業會從這些暫時性問題中自動復原。

### <a name="add-a-consumer-group-in-event-hubs"></a>在事件中樞新增取用者群組
若要在事件中樞執行個體中新增新的取用者群組，請遵循下列步驟：

1. 登入 Azure 入口網站。

2. 找到您的事件中樞。

3. 選取 [實體] 標題下方的 [事件中樞]。

4. 依名稱選取事件中樞。

5. 在 [事件中樞執行個體] 頁面上，[實體] 標題下方，選取 [取用者群組]。 隨即列出名為 **$Default** 的取用者群組。

6. 選取 [+ 取用者群組] 以新增取用者群組。 

   ![在事件中樞新增取用者群組](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. 當您在串流分析作業中建立輸入以指向事件中樞時，您已指定取用者群組。 $Default 用於未指定任何項目時。 一旦您建立新的取用者群組後，請在串流分析作業中編輯事件中樞輸入，並指定新取用者群組的名稱。


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

如需進一步的協助，請參閱我們的 [Azure Stream Analytics 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
