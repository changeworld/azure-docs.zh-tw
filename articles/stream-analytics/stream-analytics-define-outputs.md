---
title: Azure 串流分析的輸出
description: 本文說明適用于 Azure 串流分析的資料輸出選項。
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 12/9/2020
ms.openlocfilehash: 3ce4f673657561e196520466b569d0cf83d75a8a
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019341"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure 串流分析的輸出

Azure 串流分析作業是由輸入、查詢及輸出所組成。 有數種輸出類型可讓您傳送已轉換的資料。 本文列出支援的串流分析輸出。 在您設計串流分析查詢時，請使用 [INTO 子句](/stream-analytics-query/into-azure-stream-analytics)來參考輸出的名稱。 您可以針對每個作業使用單一輸出，或針對每個串流作業使用多個輸出 (如果您需要將多個 INTO 子句加入至查詢來) 這些輸出。

若要建立、編輯及測試串流分析作業輸出，您可以使用 [Azure 入口網站](stream-analytics-quick-create-portal.md#configure-job-output)、[Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job)、[.NET API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations)、[REST API](/rest/api/streamanalytics/)，以及 [Visual Studio](stream-analytics-quick-create-vs.md)。

某些輸出類型支援[資料分割](#partitioning)，且[輸出批次大小](#output-batch-size)會變化以達到最佳輸送量。 下表顯示每個輸出類型所支援的功能：

| 輸出類型 | 資料分割 | 安全性 | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|是|Azure Active Directory 使用者 </br> ，受控識別|
|[Azure SQL Database](sql-database-output.md)|是，選擇性。|SQL 使用者驗證， </br> 受控識別 (預覽)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|是|SQL 使用者驗證， </br> 受控識別 (預覽)|
|[Blob 儲存體和 Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|是|存取金鑰， </br> 受控識別 (預覽)|
|[Azure 事件中樞](event-hubs-output.md)|是，需要在輸出設定中設定分割區索引鍵資料行。|存取金鑰， </br> 受控識別 (預覽)|
|[Power BI](power-bi-output.md)|否|Azure Active Directory 使用者， </br> 受控識別|
|[Azure 資料表儲存體](table-storage-output.md)|是|帳戶金鑰|
|[Azure 服務匯流排佇列](service-bus-queues-output.md)|是|存取金鑰|
|[Azure 服務匯流排主題](service-bus-topics-output.md)|是|存取金鑰|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|是|存取金鑰|
|[Azure Functions](azure-functions-output.md)|是|存取金鑰|

## <a name="partitioning"></a>資料分割

串流分析支援所有輸出的磁碟分割，但 Power BI 除外。 如需資料分割索引鍵和輸出寫入器數目的詳細資訊，請參閱您感興趣之特定輸出類型的相關文章。 在上一節中會連結所有輸出文章。  

此外，若要更先進地微調分割區，可以使用 (查看查詢中的) 子句來控制輸出寫入器的數目 `INTO <partition count>` ，這有助於 [達成](/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 所需的作業拓撲。 如果您的輸出介面卡未分割，則在某個輸入分割區中缺少資料會導致延遲延遲到延遲的時間。 在這種情況下，輸出會合併為單一寫入器，這可能會在您的管線中產生瓶頸。 若要深入了解延遲傳入原則，請參閱 [Azure 串流分析事件的順序考量](./stream-analytics-time-handling.md)。

## <a name="output-batch-size"></a>輸出批次大小

所有輸出都支援批次處理，但只會明確支援批次大小。 Azure 串流分析使用可變大小批次來處理事件及寫入至輸出。 串流分析引擎通常不會一次撰寫一個訊息，並會使用批次以提升效率。 當傳入和傳出事件的速率很高時，串流分析會使用較大的批次。 當輸出速率較低時，它會使用較小的批次來降低延遲。

## <a name="parquet-output-batching-window-properties"></a>Parquet 輸出批次視窗屬性

使用 Azure Resource Manager 範本部署或 REST API 時，兩個批次處理視窗屬性為：

1. *timeWindow*

   每個批次的等候時間上限。 此值應該是 Timespan 的字串。 例如，"00:02:00" 表示兩分鐘。 在這段時間之後，即使不符合最小資料列需求，也會將批次寫入輸出。 預設值為1分鐘，允許的最大值為2小時。 如果您的 Blob 輸出具有路徑模式頻率，則等候時間不得高於分割區時間範圍。

2. *sizeWindow*

   這是每一批次中的資料列數目下限。 針對 Parquet，每個批次都會建立新的檔案。 目前的預設值為 2,000 個資料列，允許的上限為 10,000 個資料列。

只有 API 版本 **2017-04-01-preview** 支援這些批次處理視窗屬性。 以下是 REST API 呼叫的 JSON 承載範例：

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>
> [快速入門：使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
