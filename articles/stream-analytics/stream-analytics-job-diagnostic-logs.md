---
title: 使用資源記錄針對 Azure 串流分析進行疑難排解
description: 本文說明如何分析 Azure 串流分析中的資源記錄。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 40b57af95f9ea4d4212756634c721ddd55f85d7b
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/24/2020
ms.locfileid: "82127759"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-resource-logs"></a>使用資源記錄針對 Azure 串流分析進行疑難排解

有時候，Azure 串流分析作業會非預期地停止處理。 請務必要設法解決這類事件的問題。 錯誤發生的原因可能是非預期的查詢結果、裝置的連線狀況，或未預期的服務中斷。 串流分析中的資源記錄可協助您識別發生問題的原因，並縮短復原時間。

強烈建議您啟用所有作業的資源記錄，因為這會大幅協助進行調試和監視。

## <a name="log-types"></a>記錄類型

串流分析提供開兩種記錄類型：

* [活動記錄](../azure-monitor/platform/platform-logs-overview.md) (永遠啟動) 可讓您對所執行作業有深入的了解。

* [資源記錄](../azure-monitor/platform/platform-logs-overview.md)（可設定），可讓您更深入瞭解作業所發生的一切。 資源記錄會在作業建立時開始，並在作業刪除時結束。 這些記錄涵蓋作業更新時與作業執行時的情形。

> [!NOTE]
> 您可以使用 Azure 儲存體、Azure 事件中樞和 Azure 監視器記錄等服務來分析不一致的資料。 這些服務將會依據各自的計價模式向您收取費用。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>使用活動記錄進行偵錯

活動記錄預設會開啟，可以對 Stream Analytics 作業所執行的作業提供高層級的見解。 活動記錄中出現的資訊可能有助於找出影響您工作之問題的根本原因。 執行下列步驟，以在 Stream Analytics 中使用活動記錄：

1. 登入 Azure 入口網站，並在 [概觀]**** 下選取 [活動記錄]****。

   ![Stream Analytics 活動記錄](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. 您可以看到一份已執行的作業清單。 任何造成您的作業 (job) 失敗的任何作業 (operation) 都會出現紅色的資訊泡泡。

3. 按一下作業，以查看其摘要檢視。 這裡的資訊通常是有限的。 若要深入了解作業的相關資訊，請按一下 [JSON]****。

   ![Stream Analytics 活動記錄作業摘要](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. 向下捲動至 JSON 的 [屬性]**** 區段，該區段提供導致作業失敗的錯誤的詳細資料。 在此範例中，失敗是由於超出範圍的緯度值的執行階段錯誤。 串流分析作業所處理之資料的差異會造成資料錯誤。 您可以瞭解不同的[輸入和輸出資料錯誤，以及它們的發生原因](https://docs.microsoft.com/azure/stream-analytics/data-errors)。

   ![JSON 錯誤詳細資料](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. 您可以根據 JSON 中的錯誤訊息採取修正動作。 在此範例中，需要檢查以確保緯度值是介於 -90 度和 90 度之間。

6. 如果活動記錄中的錯誤訊息對於識別根本原因沒有説明，請啟用資源記錄，並使用 Azure 監視器記錄。

## <a name="send-diagnostics-to-azure-monitor-logs"></a>將診斷傳送至 Azure 監視器記錄

強烈建議您開啟資源記錄，並將它們傳送至 Azure 監視器記錄。 預設為**關閉**。 若要開啟它們，請完成下列步驟：

1.  登入 Azure 入口網站，然後瀏覽至您的 Stream Analytics 作業。 在 [監視]**** 下，選取 [診斷記錄]****。 然後選取 [開啟診斷]****。

    ![流覽至資源記錄的 Blade](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  在 [診斷設定]**** 中建立 [名稱]****，然後核取 [傳送至 Log Analytics]**** 旁邊的方塊。 然後，新增現有的 Log Analytics 工作區或建立新的 **Log Analytics 工作區**。 核取 [記錄]**** 下的 [執行]**** 和 [編寫]****，以及 [計量]**** 下的 [AllMetrics]**** 核取方塊。 按一下 **[儲存]** 。 建議您在與串流分析作業相同的 Azure 區域中使用 Log Analytics 工作區，以避免額外的成本。

    ![資源記錄的設定](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. 當您的串流分析作業開始時，資源記錄會路由傳送至您的 Log Analytics 工作區。 若要查看作業的資源記錄，請選取 [**監視**] 區段下的 [**記錄**]。

   ![監視中的資源記錄](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. 串流分析提供預先定義的查詢，可讓您輕鬆地搜尋您感興趣的記錄。 3個類別是**一般**、**輸入資料錯誤**和**輸出資料錯誤**。 例如，若要查看過去7天內作業的所有錯誤摘要，您可以選取 [**執行**適當的預先定義查詢]。 

   ![監視中的資源記錄](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![記錄檔的結果](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="resource-log-categories"></a>資源記錄檔類別

Azure 串流分析會捕獲兩種資源記錄類別：

* **撰寫**：捕捉與作業撰寫作業相關的記錄事件，例如工作建立、新增和刪除輸入與輸出、新增及更新查詢，以及啟動或停止作業。

* **執行**：捕捉在作業執行期間發生的事件。
    * 連線錯誤
    * 資料處理錯誤，包括：
        * 不符合查詢定義的事件 (不相符的欄位類型與值或遺漏欄位等)
        * 運算式評估錯誤
    * 其他事件和錯誤

## <a name="resource-logs-schema"></a>資源記錄架構

所有記錄會儲存為 JSON 格式。 每個項目皆包含下列常見的字串欄位︰

名稱 | 描述
------- | -------
time | 記錄的時間戳記 (UTC 時間)。
resourceId | 作業執行資源的識別碼 (大寫)。 其中包含訂用帳戶識別碼、資源群組，以及作業名稱。 例如，**/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**。
category | 記錄類別 (**執行**或**編寫**)。
operationName | 記錄的作業名稱。 例如，**傳送事件： SQL 輸出寫入失敗到 mysqloutput**。
status | 作業的狀態。 例如，**失敗**或**成功**。
層級 | 記錄層級。 例如，**錯誤**、**警告**或**資訊**。
properties | 記錄項目特定詳細資料 (序列化為 JSON 字串)。 如需詳細資訊，請參閱本文中下列幾節。

### <a name="execution-log-properties-schema"></a>執行記錄屬性結構描述

執行記錄包含在執行串流分析作業期間所發生事件的資訊。 屬性的架構會根據事件是資料錯誤還是一般事件而有所不同。

### <a name="data-errors"></a>資料錯誤

作業處理資料時發生的任何錯誤皆包含於此類記錄中。 這些記錄最常於資料讀取、序列化和寫入作業時建立。 這些記錄不包含連線錯誤。 連線錯誤視為一般事件。 您可以深入瞭解各種不同[輸入和輸出資料錯誤](https://docs.microsoft.com/azure/stream-analytics/data-errors)的原因。

名稱 | 描述
------- | -------
來源 | 發生錯誤的作業輸入或輸出名稱。
訊息 | 與錯誤相關的訊息。
類型 | 錯誤的類型。 例如，**DataConversionError**、**CsvParserError** 或 **ServiceBusPropertyColumnMissingError**。
資料 | 包含有助於正確找到錯誤來源的資料。 視其大小，資料可能會遭到截斷。

資料錯誤會隨 **operationName** 值的不同而有下列結構描述：

* **序列化事件**會在事件讀取作業期間發生。 當輸入上的資料不符合查詢結構描述時就會發生這類事件，發生原因可能是下列其中一個：

   * *事件 (還原) 序列化期間類型不相符*：找出造成錯誤的欄位。

   * *無法讀取事件，序列化無效*：針對發生錯誤的輸入資料位置列出資訊。 包含 Blob 輸入的 Blob 名稱、位移和資料範例。

* **傳送事件**會在寫入作業期間發生。 這些事件會識別造成錯誤的串流事件。

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
