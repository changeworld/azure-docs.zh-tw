---
title: 將成本與 RU/秒優化以在 Azure Cosmos DB 中執行查詢
description: 了解如何評估查詢的要求單位費用，並在效能和成本方面最佳化查詢。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6ff67e3c1588f6671173d6fa1eec3dc15883291
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020910"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化查詢成本

Azure Cosmos DB 會提供一組豐富的資料庫作業，包括對容器內的項目進行關聯式和階層式的查詢。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位 (RU) 想成是執行各種資料庫作業以服務要求時所需的資源數量。 這篇文章說明如何評估查詢的要求單位費用，並在效能和成本方面最佳化查詢。

從輸送量的角度來看，在 Azure Cosmos DB 中的讀取通常會從最快/最有效率到較慢/較低的效率，如下所示：  

* 點讀取 (單一專案識別碼和分割區索引鍵) 的索引鍵/值查閱。

* 單一分割區索引鍵內具有篩選子句的查詢。

* 任何屬性上不具等號比較或範圍篩選子句的查詢。

* 不含篩選的查詢。

由於專案識別碼上的索引鍵/值查閱是最有效率的讀取類型，因此您應該確定專案識別碼具有有意義的值。

從一或多個分割區讀取資料的查詢會產生較高的延遲，並使用更多的要求單位。 由於每個分割區都會針對所有屬性自動編製索引，因此就能有效率地從索引中提供查詢。 您可以使用平行處理原則選項，更快速地進行使用多個分割區的查詢。 若要深入了解分割區和分割區索引鍵，請參閱[在 Azure Cosmos DB 中進行資料分割](partitioning-overview.md)。

## <a name="evaluate-request-unit-charge-for-a-query"></a>評估查詢的要求單位費用

將一些資料儲存在 Azure Cosmos 容器中後，可以使用 Azure 入口網站中的 [資料總管] 來建構及執行您的查詢。 您也可以使用 [資料總管] 取得查詢的成本。 這個方法可讓您了解系統支援的典型查詢和作業所涉及的實際費用。

您還可以使用 SDK 以程式設計方式取得查詢的成本。 若要測量任何作業 (如建立、更新或刪除) 的額外負荷，請在使用 REST API 時檢查 `x-ms-request-charge` 標頭。 如果您使用 .NET 或 JAVA SDK， `RequestCharge` 屬性會是取得要求費用的對等屬性，而此屬性會出現在 ResourceResponse 或 FeedResponse 中。

```csharp
// Measure the performance (request units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>影響查詢的要求單位費用的因素

查詢的要求單位取決於許多因素。 例如，已載入/傳回的 Azure Cosmos 專案數目、針對索引的查閱次數、查詢編譯時間等詳細資料。 Azure Cosmos DB 保證在相同資料上執行時，相同的查詢將一律使用相同數量的要求單位，即使重複執行也是如此。 使用查詢執行計量的查詢設定檔，可讓您清楚了解要求單位的使用情況。  

在某些情況下，您可能會在分頁式查詢執行中看到一連串的 200 和 429 回應以及變數的要求單位，這是因為查詢會根據可用的 RU 盡快執行。 您可能會看到查詢執行分成伺服器和用戶端之間的多個頁面/來回行程。 例如，10,000 個項目可能會以多個頁面傳回，每個頁面根據該頁面上執行的計算來收費。 當您加總這些頁面時，您應該取得與整個查詢相同的 RU 數。  

## <a name="metrics-for-troubleshooting"></a>用於疑難排解的計量

由查詢、使用者定義函數 (UDF) 耗用的效能和輸送量主要取決於函式主體。 找出 UDF 中查詢執行花費的時間以及消耗的 RU 數目，最簡單的方式是啟用查詢計量。 如果您使用 .NET SDK，以下是 SDK 所傳回的範例查詢計量：

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>最佳化查詢成本的最佳做法 

在最佳化查詢成本時，請考慮下列最佳做法：

* **共置多個實體類型**

   嘗試在單一或更少數量的容器內共置多個實體類型。 此方法不僅可以從定價的觀點獲益，還可以從查詢執行和交易中獲益。 查詢範圍侷限於單一容器；透過預存程序/觸發程序將多個記錄上不可部分完成交易設定為單一容器內的資料分割索引鍵。 在相同容器中共置實體可以減少網路來回行程次數，以解析記錄之間的關聯性。 因此，它可以提高端對端效能、為較大的資料集啟用多個記錄的不可部分完成交易，從而降低成本。 如果在您的案例中，難以在單一或更少數量的容器中共置多個實體類型，通常是因為您正在移轉現有的應用程式，且您不希望進行任何程式碼變更 - 則您應該考慮佈建資料庫層級的輸送量。  

* **測量和調整較低的要求單位/秒使用量**

   查詢的複雜性會影響針對作業所耗用的要求單位 (RU) 數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小。 所有這些因素都會影響查詢作業的成本。 

   要求標頭中傳回的要求費用表示給定查詢的成本。 例如，如果查詢傳回 1000 個 1 KB 項目，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)一文和要求單位計算機。 

## <a name="next-steps"></a>後續步驟

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解 [Azure Cosmos 定價的運作方式](how-pricing-works.md)
* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
* 深入瞭解 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)

