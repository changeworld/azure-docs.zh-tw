---
title: 設定錯誤碼 - Azure 串流分析
description: 針對出現設定錯誤碼的 Azure 串流分析問題進行疑難排解。
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 9951a6ddfbd1c562184e2b7e02a9da98f7d94b12
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595587"
---
# <a name="azure-stream-analytics-configuration-error-codes"></a>Azure 串流分析設定錯誤碼

您可使用活動記錄和資源記錄，協助對 Azure 串流分析作業中的非預期行為進行偵錯。 本文列出每個設定錯誤碼的描述。 設定錯誤與您的作業設定或輸入和輸出設定相關。

## <a name="kafkainvalidpermission"></a>KafkaInvalidPermission

* **原因**︰串流分析作業沒有適當的權限可執行某些動作。

## <a name="kafkainvalidconfiguration"></a>KafkaInvalidConfiguration

* **原因**︰Kafka 配接器上的某些組態無效。

## <a name="kafkainvalidtimestamptype"></a>KafkaInvalidTimestampType

* **原因**︰Kafka 所使用的時間戳記類型無效。

## <a name="eventhubunauthorizedaccess"></a>EventHubUnauthorizedAccess

* **原因**︰事件中樞擲回「未經授權存取」錯誤。

## <a name="eventhubreceiverepochconflict"></a>EventHubReceiverEpochConflict

* **原因**︰有一個以上具有不同 Epoch 值的事件中樞接收器。
* **建議**：確保當串流分析作業正在執行時，Service Bus Explorer 或 EventProcessorHost 應用程式並未連線。

## <a name="eventhubreceiverquotaexceeded"></a>EventHubReceiverQuotaExceeded

* **原因**︰串流分析無法連線到分割區，因為已達到取用者群組中每個分割區允許的接收者數目上限。
* **建議**：確保其他串流分析作業或 Service Bus Explorer 並未使用相同的取用者群組。

## <a name="eventhuboutputthrottled"></a>EventHubOutputThrottled

* **原因**︰因為節流以致將資料寫入到事件中樞時發生錯誤。
* **建議**：如果這種情況持續發生，請將輸送量升級。

## <a name="eventhuboutputinvalidconnectionconfig"></a>EventHubOutputInvalidConnectionConfig

* **原因**︰所提供的連線設定不正確。
* **建議**：更正設定，然後重新開始作業。

## <a name="eventhuboutputinvalidhostname"></a>EventHubOutputInvalidHostname

* **原因**︰無法連線到事件中樞主機。
* **建議**：確保所提供的主機名稱正確無誤。

## <a name="eventhuboutputunexpectedpartitioncount"></a>EventHubOutputUnexpectedPartitionCount

* **原因**︰EventHub 傳送者遇到非預期的 EventHub 分割區計數。
* **建議**：如果 EventHub 的分割區計數已變更，請重新開始串流分析作業。

## <a name="cosmosdbpartitionkeynotfound"></a>CosmosDBPartitionKeyNotFound

* **原因**︰串流分析在資料庫中找不到特定 Cosmos DB 集合的分割區索引鍵。
* **建議**：確保為 Cosmos DB 中的集合指定了有效的分割區索引鍵。

## <a name="cosmosdbinvalidpartitionkeycolumn"></a>CosmosDBInvalidPartitionKeyColumn

* **原因**︰當分割區索引鍵既不是分葉節點，也不是最上層時擲回。

## <a name="cosmosdbinvalididcolumn"></a>CosmosDBInvalidIdColumn

* **原因**︰如果已選擇不同的資料行作為主索引鍵屬性，查詢輸出就不能包含資料行 \[id]。

## <a name="cosmosdbdatabasenotfound"></a>CosmosDBDatabaseNotFound

* **原因**︰串流分析找不到 CosmosDB 資料庫。

## <a name="cosmosdbcollectionnotfound"></a>CosmosDBCollectionNotFound

* **原因**︰串流分析在資料庫中找不到特定的 Cosmos DB 集合。

## <a name="cosmosdboutputwritethrottling"></a>CosmosDBOutputWriteThrottling

* **原因**︰由於 Cosmos DB 節流以致寫入資料時發生錯誤。
* **建議**：升級集合效能層級並調整資料庫的效能。

## <a name="sqldatabaseconnectionstringerror"></a>SQLDatabaseConnectionStringError

* **原因**︰串流分析作業發生驗證錯誤。
* **建議**：確保 SQL Database 連接字串正確無效。

## <a name="sqldatabasemanagedidentityauthenticationerror"></a>SQLDatabaseManagedIdentityAuthenticationError

* **原因**︰串流分析作業發生驗證錯誤。 
* **建議**：確保帳戶名稱設定正確無誤，且作業的受控識別可存取 SQL Database。

## <a name="sqldatabaseoutputnotableerror"></a>SQLDatabaseOutputNoTableError

* **原因**︰串流分析找不到特定資料表的結構描述資訊。

## <a name="sqldwoutputinvalidserviceedition"></a>SQLDWOutputInvalidServiceEdition

* **原因**︰不支援 SQL Database。
* **建議**：使愈 Synapse SQL 集區。

## <a name="next-steps"></a>後續步驟

* [對輸入連線進行疑難排解](stream-analytics-troubleshoot-input.md)
* [對 Azure 串流分析輸出進行疑難排解](stream-analytics-troubleshoot-output.md)
* [對 Azure 串流分析查詢進行疑難排解](stream-analytics-troubleshoot-query.md)
* [使用資料記錄對 Azure 串流分析進行疑難排解](stream-analytics-job-diagnostic-logs.md)
* [Azure 串流分析資料錯誤](data-errors.md)
