---
title: 了解 Azure 串流分析中的作業監視
description: 本文說明如何在 Azure 入口網站中監視 Azure 串流分析作業。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2018
ms.custom: seodec18
ms.openlocfilehash: 11cb598420b4cecf803b17a78f14f9268e1c8be2
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83831274"
---
# <a name="understand-stream-analytics-job-monitoring-and-how-to-monitor-queries"></a>了解串流分析工作監視功能，以及如何監視查詢

## <a name="introduction-the-monitor-page"></a>簡介：監視器頁面
Azure 入口網站會顯示關鍵效能計量，可供您用來監視查詢和工作效能並進行疑難排解。 若要查看這些計量，請瀏覽至您有興趣查看計量的「串流分析」工作，然後檢視 [概觀] 頁面上的 [監視] 區段。  

![串流分析作業監視連結](./media/stream-analytics-monitoring/02-stream-analytics-monitoring-block.png)

視窗將會出現，如下所示：

![串流分析作業監視儀表板](./media/stream-analytics-monitoring/01-stream-analytics-monitoring.png)  

## <a name="metrics-available-for-stream-analytics"></a>可供串流分析使用的度量
| 計量                 | 定義                               |
| ---------------------- | ---------------------------------------- |
| 待處理輸入事件數       | 待處理的輸入事件數目。 此計量的非零值表示您的工作無法跟上內送事件數量。 如果這個值會緩慢增加或始終為非零，則您應該擴增您的作業。 您可以透過瀏覽[了解及調整串流單位](stream-analytics-streaming-unit-consumption.md)來深入了解。 |
| 資料轉換錯誤 | 無法轉換為預期輸出結構描述的輸出事件數目。 可以將錯誤原則變更為 'Drop' 來卸除遇到此狀況的事件。 |
| 早期輸入事件       | 應用程序時間戳記早於其抵達時間超過 5 分鐘的事件。 |
| 失敗的函式要求 | 失敗的 Azure Machine Learning 函式呼叫次數 (如果有的話)。 |
| 函式事件        | 傳送給 Azure Machine Learning 函式的事件數目 (如果有的話)。 |
| 函式要求      | 對 Azure Machine Learning 函式發出的呼叫次數 (如果有的話)。 |
| 輸入還原序列化錯誤       | 無法還原序列化的輸入事件數目。  |
| 輸入事件位元組      | 「串流分析」工作所接收到的資料量 (以位元組為單位)。 這可以用來驗證傳送到輸入來源的事件。 |
| 輸入事件           | 從輸入事件還原序列化的記錄數目。 此計數不包括導致還原序列化錯誤的傳入事件。 串流分析可以在內部復原和自我聯結之類情節中多次內嵌相同的事件。 因此，如果您的作業有簡單的「通過」查詢，建議您不要預期輸入事件和輸出事件計量會進行比對。 |
| 收到的輸入來源數       | 作業收到的訊息數。 若為事件中樞，訊息是單一 EventData。 若為 Blob，訊息是單一 Blob。 請注意，輸入來源會在還原序列化之前計數。 如果有還原序列化錯誤，輸入來源可能會大於輸入事件。 否則，其可能小於或等於輸入事件，因為每則訊息都可以包含多個事件。 |
| 延遲輸入事件      | 晚於已設定延遲傳入容錯時間抵達的事件。 深入了解 [Azure 串流分析事件的順序考量](stream-analytics-out-of-order-and-late-events.md)。 |
| 順序錯亂事件    | 所收到順序錯亂的事件數目，這些事件會根據事件順序原則，予以捨棄或指定調整後的時間戳記。 順序錯亂容錯視窗設定的組態可能會造成影響。 |
| 輸出事件          | 「串流分析」工作所傳送的資料量 (以事件數為單位)。 |
| 執行階段錯誤         | 與查詢處理相關的錯誤總數 (不包括在擷取事件或輸出結果時發現的錯誤) |
| SU % 使用率       | 從工作的 [調整] 索引標籤指派給工作的串流處理單元使用率。 若此指標達到 80% 以上，則代表事件處理作業極有可能延遲或暫停。 |
| 浮水印延遲秒數       | 作業中所有輸出分割區的延遲秒數上限。 |

您可以使用這些計量來[監視串流分析作業的效能](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor)。 

## <a name="customizing-monitoring-in-the-azure-portal"></a>在 Azure 入口網站中自訂監視
您可以在 [編輯圖表] 設定中調整圖表類型、顯示的度量和時間範圍。 如需詳細資料，請參閱[如何自訂監視](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md)。

  ![串流分析查詢監視時間圖](./media/stream-analytics-monitoring/08-stream-analytics-monitoring.png)  


## <a name="latest-output"></a>最新的輸出
另一個監視作業有趣的資料點是上次輸出的時間，如 [概觀] 頁面中所示。
此時間是作業最新輸出的應用時間 (亦即，使用事件資料之時間戳記的時間)。

## <a name="get-help"></a>取得說明
如需進一步的協助，請嘗試 [Azure 串流分析的 Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>後續步驟
* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
