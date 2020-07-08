---
title: 資料錯誤碼 - Azure 串流分析
description: 針對出現資料錯誤碼的 Azure 串流分析問題進行疑難排解。
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 56d7527eebb91bd09895f6cd0238721574df1015
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037319"
---
# <a name="azure-stream-analytics-data-error-codes"></a>Azure 串流分析資料錯誤代碼

您可使用活動記錄和資源記錄，協助對 Azure 串流分析作業中的非預期行為進行偵錯。 本文列出每個資料錯誤錯誤碼的描述。 當資料流中有不良資料 (例如非預期的記錄結構描述) 時，就會發生資料錯誤。

## <a name="inputdeserializationerror"></a>InputDeserializationError

* **原因**︰還原序列化輸入資料時發生錯誤。

## <a name="inputeventtimestampnotfound"></a>InputEventTimestampNotFound

* **原因**︰串流分析無法取得資源的時間戳記。 

## <a name="inputeventtimestampbyovervaluenotfound"></a>InputEventTimestampByOverValueNotFound

* **原因**︰串流分析無法取得 `TIMESTAMP BY OVER COLUMN` 的值。

## <a name="inputeventlatebeyondthreshold"></a>InputEventLateBeyondThreshold

* **原因**︰輸入事件的傳送時間晚於所設定的容錯。

## <a name="inputeventearlybeyondthreshold"></a>InputEventEarlyBeyondThreshold

* **原因**︰輸入事件抵達時間早於輸入事件應用程式時間戳記閾值。

## <a name="azurefunctionmessagesizeexceeded"></a>AzureFunctionMessageSizeExceeded

* **原因**︰對 Azure Functions 的訊息輸出超出大小限制。

## <a name="eventhuboutputrecordexceedssizelimit"></a>EventHubOutputRecordExceedsSizeLimit

* **原因**︰寫入事件中樞時，輸出記錄超過大小上限。

## <a name="cosmosdboutputinvalidid"></a>CosmosDBOutputInvalidId

* **原因**︰特定資料行的值或類型無效。
* **建議**：請提供不超過 255 個字元的唯一非空白字串。

## <a name="cosmosdboutputinvalididcharacter"></a>CosmosDBOutputInvalidIdCharacter

* **原因**︰輸出記錄的文件識別碼包含無效字元。

## <a name="cosmosdboutputmissingid"></a>CosmosDBOutputMissingId

* **原因**︰輸出記錄不包含用來作為主索引鍵屬性的資料行 \[id]。

## <a name="cosmosdboutputmissingidcolumn"></a>CosmosDBOutputMissingIdColumn

* **原因**︰輸出記錄不包含文件識別碼屬性。 
* **建議**：請確定查詢輸出包含的資料行具有小於 '255' 個字元的唯一非空白字串。

## <a name="cosmosdboutputmissingpartitionkey"></a>CosmosDBOutputMissingPartitionKey

* **原因**︰輸出記錄缺少要作為分割區索引鍵屬性的資料行。

## <a name="cosmosdboutputsinglerecordtoolarge"></a>CosmosDBOutputSingleRecordTooLarge

* **原因**︰寫入 Cosmos DB 的單一記錄太大。

## <a name="sqldatabaseoutputdataerror"></a>SQLDatabaseOutputDataError

* **原因**︰因為資料發生問題，所以串流分析無法將事件寫入 SQL Database。

## <a name="next-steps"></a>後續步驟

* [對輸入連線進行疑難排解](stream-analytics-troubleshoot-input.md)
* [對 Azure 串流分析輸出進行疑難排解](stream-analytics-troubleshoot-output.md)
* [對 Azure 串流分析查詢進行疑難排解](stream-analytics-troubleshoot-query.md)
* [使用資料記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)
* [Azure 串流分析資料錯誤](data-errors.md)
