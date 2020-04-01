---
title: 使用析診斷記錄針對 Azure 串流分進行疑難排解
description: 本文說明如何分析 Azure 串流分析中的診斷記錄。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398413"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>使用析診斷記錄對 Azure 串流分進行疑難排解

有時候，Azure 串流分析作業會非預期地停止處理。 請務必要設法解決這類事件的問題。 錯誤發生的原因可能是非預期的查詢結果、裝置的連線狀況，或未預期的服務中斷。 串流分析中的診斷記錄可協助您在事件發生當下找出問題原因，並減少復原時間。

強烈建議為所有作業啟用診斷日誌,因為這將極大地幫助調試和監視。

## <a name="log-types"></a>記錄類型

串流分析提供開兩種記錄類型：

* [活動記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (永遠啟動) 可讓您對所執行作業有深入的了解。

* [診斷記錄](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (可設定) 可讓您更深入地了所有與作業相關的發生事件。 診斷記錄會在作業建立時開始執行，並在作業刪除時結束。 這些記錄涵蓋作業更新時與作業執行時的情形。

> [!NOTE]
> 可以使用 Azure 存儲、Azure 事件中心和 Azure 監視器日誌等服務來分析不符合項數據。 這些服務將會依據各自的計價模式向您收取費用。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>使用活動記錄進行偵錯

活動記錄預設會開啟，可以對 Stream Analytics 作業所執行的作業提供高層級的見解。 活動記錄中出現的資訊可能有助於找出影響您工作之問題的根本原因。 執行下列步驟，以在 Stream Analytics 中使用活動記錄：

1. 登入 Azure 入口網站，並在 [概觀]**** 下選取 [活動記錄]****。

   ![Stream Analytics 活動記錄](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 您可以看到一份已執行的作業清單。 任何造成您的作業 (job) 失敗的任何作業 (operation) 都會出現紅色的資訊泡泡。

3. 按一下作業，以查看其摘要檢視。 這裡的資訊通常是有限的。 若要深入了解作業的相關資訊，請按一下 [JSON]****。

   ![Stream Analytics 活動記錄作業摘要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 向下捲動至 JSON 的 [屬性]**** 區段，該區段提供導致作業失敗的錯誤的詳細資料。 在此範例中，失敗是由於超出範圍的緯度值的執行階段錯誤。 流分析作業處理的數據差異會導致數據錯誤。 您可以瞭解不同的[輸入與輸出資料錯誤及其發生的原因](https://docs.microsoft.com/azure/stream-analytics/data-errors)。

   ![JSON 錯誤詳細資料](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. 您可以根據 JSON 中的錯誤訊息採取修正動作。 在此範例中，需要檢查以確保緯度值是介於 -90 度和 90 度之間。

6. 如果活動日誌中的錯誤消息對識別根本原因沒有説明,則啟用診斷日誌並使用 Azure 監視器日誌。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>將診斷傳送 Azure 監視器紀錄

強烈建議打開診斷日誌並將其發送到 Azure 監視器日誌。 診斷記錄預設為 [關閉]****。 若要開啟診斷記錄，請完成下列步驟︰

1.  登入 Azure 入口網站，然後瀏覽至您的 Stream Analytics 作業。 在 [監視]**** 下，選取 [診斷記錄]****。 然後選取 [開啟診斷]****。

    ![瀏覽到診斷記錄的刀鋒視窗](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  在 [診斷設定]**** 中建立 [名稱]****，然後核取 [傳送至 Log Analytics]**** 旁邊的方塊。 然後，新增現有的 Log Analytics 工作區或建立新的 **Log Analytics 工作區**。 核取 [記錄]**** 下的 [執行]**** 和 [編寫]****，以及 [計量]**** 下的 [AllMetrics]**** 核取方塊。 按一下 [儲存]****。 建議在與流分析作業相同的 Azure 區域中使用日誌分析工作區,以防止增加成本。

    ![診斷記錄的設定](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. 當您的 Stream Analytics 作業啟動時，診斷記錄會路由傳送至 Log Analytics 工作區。 要查看作業的診斷日誌,請在 **「監視**」部分下選擇 **「日誌**」。。

   ![監控下的診斷紀錄](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. 串流分析提供預先定義的查詢,使您能夠輕鬆搜尋您感興趣的日誌。 這 3 個類別是**一般**、**輸入資料錯誤**與**輸出資料錯誤**。 例如,要查看過去 7 天內作業的所有錯誤的摘要,可以選擇 **「運行**適當的預定義查詢」。 

   ![監控下的診斷紀錄](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![紀錄結果](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>診斷記錄類別

Azure 流分析捕獲兩類診斷日誌:

* **創作**:捕獲與作業創作操作相關的日誌事件,例如創建作業、添加和刪除輸入和輸出、添加和更新查詢以及啟動或停止作業。

* **執行**:捕獲作業執行期間發生的事件。
    * 連線錯誤
    * 資料處理錯誤，包括：
        * 不符合查詢定義的事件 (不相符的欄位類型與值或遺漏欄位等)
        * 運算式評估錯誤
    * 其他事件和錯誤

## <a name="diagnostics-logs-schema"></a>診斷記錄結構描述

所有記錄會儲存為 JSON 格式。 每個項目皆包含下列常見的字串欄位︰

名稱 | 描述
------- | -------
time | 記錄的時間戳記 (UTC 時間)。
resourceId | 作業執行資源的識別碼 (大寫)。 其中包含訂用帳戶識別碼、資源群組，以及作業名稱。 例如，**/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**。
category | 記錄類別 (**執行**或**編寫**)。
operationName | 記錄的作業名稱。 例如,**傳送事件:SQL 輸出寫入失敗到 mysql輸出**。
status | 作業的狀態。 例如，**失敗**或**成功**。
層級 | 記錄層級。 例如，**錯誤**、**警告**或**資訊**。
properties | 記錄項目特定詳細資料 (序列化為 JSON 字串)。 如需詳細資訊，請參閱本文中下列幾節。

### <a name="execution-log-properties-schema"></a>執行記錄屬性結構描述

執行記錄包含在執行串流分析作業期間所發生事件的資訊。 屬性的架構因事件是數據錯誤還是泛型事件而異。

### <a name="data-errors"></a>資料錯誤

作業處理資料時發生的任何錯誤皆包含於此類記錄中。 這些記錄最常於資料讀取、序列化和寫入作業時建立。 這些記錄不包含連線錯誤。 連線錯誤視為一般事件。 您可以瞭解有關各種不同[輸入和輸出資料錯誤](https://docs.microsoft.com/azure/stream-analytics/data-errors)的原因。

名稱 | 描述
------- | -------
來源 | 發生錯誤的作業輸入或輸出名稱。
訊息 | 與錯誤相關的訊息。
類型 | 錯誤的類型。 例如，**DataConversionError**、**CsvParserError** 或 **ServiceBusPropertyColumnMissingError**。
資料 | 包含有助於正確找到錯誤來源的資料。 視其大小，資料可能會遭到截斷。

資料錯誤會隨 **operationName** 值的不同而有下列結構描述：

* **序列化事件**發生在事件讀取操作期間。 當輸入上的資料不符合查詢結構描述時就會發生這類事件，發生原因可能是下列其中一個：

   * *事件 (還原) 序列化期間類型不相符*：找出造成錯誤的欄位。

   * *無法讀取事件，序列化無效*：針對發生錯誤的輸入資料位置列出資訊。 包含 Blob 輸入的 Blob 名稱、位移和資料範例。

* **發送事件**在寫入操作期間發生。 這些事件會識別造成錯誤的串流事件。

### <a name="generic-events"></a>一般事件

一般事件涵蓋所有其他事件。

名稱 | 描述
-------- | --------
錯誤 | (選用) 錯誤資訊。 這通常是例外狀況資訊 (如果有的話)。
訊息| 記錄訊息。
類型 | 訊息類型。 對應至錯誤的內部分類。 例如，**JobValidationError**或 **BlobOutputAdapterInitializationFailure**。
相互關連識別碼 | 唯一識別作業執行的 [GUID (英文)](https://en.wikipedia.org/wiki/Universally_unique_identifier)。 從作業開始直到作業停止的所有執行記錄項目皆具有同一個**相互關聯識別碼**值。

## <a name="next-steps"></a>後續步驟

* [串流分析介紹](stream-analytics-introduction.md)
* [開始使用串流分析](stream-analytics-real-time-fraud-detection.md)
* [調整串流分析作業](stream-analytics-scale-jobs.md)
* [串流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [串流分析資料錯誤](https://docs.microsoft.com/azure/stream-analytics/data-errors)
