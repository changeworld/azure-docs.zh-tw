---
title: 外部錯誤碼 - Azure 串流分析
description: 針對出現外部錯誤碼的 Azure 串流分析問題進行疑難排解。
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 61d6556467bc6f1494e11ce3a01bf5266b740f3f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305788"
---
# <a name="azure-stream-analytics-external-error-codes"></a>Azure 串流分析外部錯誤碼

您可使用活動記錄和資源記錄，協助對 Azure 串流分析作業中的非預期行為進行偵錯。 本文列出每個外部錯誤碼的描述。 外部錯誤是上游或下游服務所擲回的一般錯誤，而串流分析無法這類錯誤區分為資料錯誤、設定錯誤或外部可用性錯誤。

## <a name="adapterinitializationerror"></a>AdapterInitializationError

* **原因** ︰初始化配接器時發生錯誤。

## <a name="adapterfailedtowriteevents"></a>AdapterFailedToWriteEvents

* **原因** ︰將資料寫入到配接器時發生錯誤。

## <a name="azurefunctionhttperror"></a>AzureFunctionHttpError

* **原因** ︰Azure Functions 傳回了 HTTP 錯誤。

## <a name="azurefunctionfailedtosendmessage"></a>AzureFunctionFailedToSendMessage

* **原因** ︰串流分析無法將事件寫入到 Azure Function。

## <a name="azurefunctionredirecterror"></a>AzureFunctionRedirectError

* **原因** ︰輸出至 Azure Functions 時發生重新導向錯誤。

## <a name="azurefunctionclienterror"></a>AzureFunctionClientError

* **原因** ︰輸出至 Azure Functions 時發生用戶端錯誤。

## <a name="azurefunctionservererror"></a>AzureFunctionServerError

* **原因** ︰輸出至 Azure Functions 時發生伺服器錯誤。

## <a name="azurefunctionhttptimeouterror"></a>AzureFunctionHttpTimeOutError

* **原因** ︰因為 HTTP 要求超過逾時，所以寫入至 Azure Functions 失敗。 
* **建議** ：檢查您的 Azure Functions 記錄是否有潛在的延遲。

## <a name="eventhubargumenterror"></a>EventHubArgumentError

* **原因** ︰輸入位移無效。 這可能是由於容錯移轉所造成。
* **建議** ：從上次輸出時間重新開始您的串流分析作業。

## <a name="eventhubfailedtowriteevents"></a>EventHubFailedToWriteEvents

* **原因** ︰將資料傳送到事件中樞時發生錯誤。

## <a name="cosmosdbconnectionfailureaftermaxretries"></a>CosmosDBConnectionFailureAfterMaxRetries

* **原因** ︰串流分析無法在達到重試次數上限後連線到 Cosmos DB 帳戶。

## <a name="cosmosdbfailureaftermaxretries"></a>CosmosDBFailureAfterMaxRetries

* **原因** ︰串流分析無法在達到重試次數上限後查詢 Cosmos DB 資料庫和集合。

## <a name="cosmosdbfailedtocreatestoredprocedure"></a>CosmosDBFailedToCreateStoredProcedure

* **原因** ︰CosmosDB 無法在多次重試之後建立預存程序。

## <a name="cosmosdboutputrequesttimeout"></a>CosmosDBOutputRequestTimeout

* **原因** ︰Upsert 預存程序傳回了錯誤。 

## <a name="sqldatabaseoutputinitializationerror"></a>SQLDatabaseOutputInitializationError

* **原因** ︰串流分析無法初始化 SQL Database 輸出。

## <a name="sqldatabaseoutputwriteerror"></a>SQLDatabaseOutputWriteError

* **原因** ︰串流分析無法將事件寫入到 SQL Database 輸出。

## <a name="sqldwoutputinitializationerror"></a>SQLDWOutputInitializationError

* **原因** ：初始化專用的 SQL 集區輸出時發生錯誤。

## <a name="sqldwoutputwriteerror"></a>SQLDWOutputWriteError

* **原因** ：將輸出寫入專用的 SQL 集區時發生錯誤。

## <a name="next-steps"></a>後續步驟

* [對輸入連線進行疑難排解](stream-analytics-troubleshoot-input.md)
* [對 Azure 串流分析輸出進行疑難排解](stream-analytics-troubleshoot-output.md)
* [對 Azure 串流分析查詢進行疑難排解](stream-analytics-troubleshoot-query.md)
* [使用資源記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)
* [Azure 串流分析資料錯誤](data-errors.md)
