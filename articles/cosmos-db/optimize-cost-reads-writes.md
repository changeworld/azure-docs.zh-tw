---
title: 在 Azure Cosmos DB 中優化要求的成本
description: 本文說明如何在 Azure Cosmos DB 發出要求時將成本優化。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 58b57bd592ec0b302724f9339c0e0d48fed42d15
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281189"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>優化 Azure Cosmos DB 中的要求成本

本文說明如何將讀取和寫入要求轉譯為 [要求單位](request-units.md) ，以及如何將這些要求的成本優化。 讀取作業包括點讀取和查詢。 寫入作業包括插入、取代、刪除和 upsert 專案。

Azure Cosmos DB 提供一組豐富的資料庫作業，可操作容器內的專案。 與上述各項作業相關聯的成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 與其考慮和管理硬體資源，您可以將要求單位 (RU) 作為執行各種資料庫作業來處理要求所需資源的單一量值。

## <a name="measuring-the-ru-charge-of-a-request"></a>測量要求的 RU 費用

您必須測量要求的 RU 費用，以瞭解其實際成本，並評估優化的有效性。 您可以使用 Azure 入口網站來提取這項成本，或檢查透過其中一個 Sdk 從 Azure Cosmos DB 送回的回應。 如需如何達成此目標的詳細指示，請參閱 [Azure Cosmos DB 中的尋找要求單位費用](find-request-unit-charge.md) 。

## <a name="reading-data-point-reads-and-queries"></a>讀取資料：點讀取和查詢

在 Azure Cosmos DB 中，讀取作業通常會從最快/最有效率到更慢/較低的效率（以 RU 耗用量為依據），如下所示：  

* 點讀取 (單一專案識別碼和分割區索引鍵) 的索引鍵/值查閱。
* 單一分割區索引鍵內具有篩選子句的查詢。
* 任何屬性上不具等號比較或範圍篩選子句的查詢。
* 不含篩選的查詢。

### <a name="role-of-the-consistency-level"></a>一致性層級的角色

使用**強**式或**限定過期**[一致性層級](consistency-levels.md)時，任何讀取作業 (點讀取或查詢) 的 RU 成本都會加倍。

### <a name="point-reads"></a>點讀取

除了使用的一致性層級以外，只會影響點讀取 (的 RU 費用的唯一因素) 是所抓取專案的大小。 下表顯示大小為 1 KB 和 100 KB 之專案的點讀取的 RU 成本。

| **專案大小** | **讀取某個點的成本** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

由於點讀取 (專案識別碼) 的索引鍵/值查閱是最有效率的讀取類型，因此您應該確定您的專案識別碼具有有意義的值，如此一來，您就可以在可能的情況下，利用點讀取 (而非查詢) 來提取專案。

### <a name="queries"></a>查詢

查詢的要求單位取決於許多因素。 例如，已載入/傳回的 Azure Cosmos 專案數目、針對索引的查閱次數、查詢編譯時間等詳細資料。 Azure Cosmos DB 保證在相同資料上執行時，相同的查詢將一律使用相同數量的要求單位，即使重複執行也是如此。 使用查詢執行計量的查詢設定檔，可讓您清楚了解要求單位的使用情況。  

在某些情況下，您可能會在分頁式查詢執行中看到一連串的 200 和 429 回應以及變數的要求單位，這是因為查詢會根據可用的 RU 盡快執行。 您可能會看到查詢執行分成伺服器和用戶端之間的多個頁面/來回行程。 例如，10,000 個項目可能會以多個頁面傳回，每個頁面根據該頁面上執行的計算來收費。 當您加總這些頁面時，您應該取得與整個查詢相同的 RU 數。

#### <a name="metrics-for-troubleshooting-queries"></a>查詢疑難排解的計量

查詢所耗用的效能和輸送量 (包括使用者定義函數) 大多取決於函數主體。 若要瞭解在 UDF 中花費多少時間，以及取用的 ru 數目，最簡單的方式是啟用查詢度量。 如果您使用 .NET SDK，以下是 SDK 所傳回的範例查詢計量：

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

#### <a name="best-practices-to-cost-optimize-queries"></a>最佳化查詢成本的最佳做法 

在最佳化查詢成本時，請考慮下列最佳做法：

* **共置多個實體類型**

   嘗試在單一或更少數量的容器內共置多個實體類型。 此方法不僅可以從定價的觀點獲益，還可以從查詢執行和交易中獲益。 查詢範圍侷限於單一容器；透過預存程序/觸發程序將多個記錄上不可部分完成交易設定為單一容器內的資料分割索引鍵。 在相同容器中共置實體可以減少網路來回行程次數，以解析記錄之間的關聯性。 因此，它可以提高端對端效能、為較大的資料集啟用多個記錄的不可部分完成交易，從而降低成本。 如果在您的案例中，難以在單一或更少數量的容器中共置多個實體類型，通常是因為您正在移轉現有的應用程式，且您不希望進行任何程式碼變更 - 則您應該考慮佈建資料庫層級的輸送量。  

* **測量和調整較低的要求單位/秒使用量**

   查詢的複雜性會影響針對作業所耗用的要求單位 (RU) 數量。 述詞數目、述詞性質、UDF 數目，以及來源資料集的大小。 所有這些因素都會影響查詢作業的成本。 

Azure Cosmos DB 使用布建的輸送量模型，以輸送量和延遲為依據來提供可預測的效能。 佈建的輸送量以每秒[要求單位](request-units.md)或 RU/每秒表示。 要求單位 (RU) 是對執行要求所需之運算資源 (例如 CPU、記憶體、IO 等) 的邏輯抽象概念。 佈建的輸送量 (RU) 被預留並專用於您的容器或資料庫，以提供可預測的輸送量和延遲。 佈建的輸送量使 Azure Cosmos DB 能夠以任何規模提供可預測且一致的效能、保證低延遲，以及高可用性。 要求單位代表標準化的貨幣，可簡化應用程式所需之資源的數量推論。

要求標頭中傳回的要求費用表示給定查詢的成本。 例如，如果查詢傳回 1000 個 1 KB 項目，則作業成本會是 1000。 因此在一秒內，伺服器在對後續要求進行速率限制前，只會接受兩個這類要求。 如需詳細資訊，請參閱[要求單位](request-units.md)一文和要求單位計算機。

## <a name="writing-data"></a>寫入資料

寫入專案的 RU 成本取決於：

- 專案大小。
- [索引編制原則](index-policy.md)所涵蓋且需要編制索引的屬性數目。

插入具有少於5個屬性的 1 KB 專案，以編制大約5個 ru 的索引成本。 將專案成本取代為插入相同專案所需的兩倍費用。

### <a name="optimizing-writes"></a>優化寫入

將寫入作業的 RU 成本優化的最佳方式，就是時您的專案以及取得索引的屬性數目。

- 在 Azure Cosmos DB 中儲存非常大型的專案會產生高 RU 費用，並可視為反模式。 尤其是，請勿儲存不需要查詢的二進位內容或大型文字區塊。 最佳做法是將這類資料放在 [Azure Blob 儲存體](../storage/blobs/storage-blobs-introduction.md) 中，並在您寫入 Azure Cosmos DB 的專案中儲存 Blob 的參考 (或連結) 。
- 將您的索引編制原則優化，只為您的查詢所篩選的屬性編制索引，可在寫入作業所耗用的 ru 上產生顯著的差異。 建立新的容器時，預設的索引編制原則會針對您專案中所找到的每個屬性和每個屬性編制索引。 雖然這是適用于開發活動的良好預設值，但強烈建議在進入生產環境時，或當您的工作負載開始接收大量流量時，重新評估和 [自訂您的索引編制原則](how-to-manage-indexing-policy.md) 。

執行大量內嵌資料時，也建議您使用 [Azure Cosmos DB 大量執行](bulk-executor-overview.md) 程式程式庫，因為它是設計用來優化這類作業的 RU 耗用量。 （選擇性）您也可以使用建立在相同程式庫上的 [Azure Data Factory](../data-factory/introduction.md) 。

## <a name="next-steps"></a>後續步驟

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
* 深入瞭解 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)
