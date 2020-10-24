---
title: 在 Azure Cosmos DB 中獲得最佳的輸送量成本
description: 本文說明如何針對儲存在 Azure Cosmos DB 中的資料獲得最佳的輸送量成本。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: d7d77bdb223e8c3b71ef03febd4081d1f63bd1a3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475459"
---
# <a name="optimize-provisioned-throughput-cost-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中最佳化已佈建的輸送量成本

Azure Cosmos DB 可藉由提供所佈建的輸送量模型，於任何規模提供可預測的效能。 事先保留或佈建輸送量，效能就能避免出現「擾鄰效果」。 請指定您所需的確切輸送量，如此一來，Azure Cosmos DB 便可根據 SLA 來保證您所設定的輸送量。

一開始，您可以使用最小的輸送量 400 RU/秒，之後再相應增加為每秒數千萬個要求以上。 您針對 Azure Cosmos 容器或資料庫所發出的每個要求 (例如，讀取要求、寫入要求、查詢要求)，預存程序都會有對應的成本，並且會從您所佈建的輸送量扣除。 如果您佈建 400 RU/秒，並發出成本為 40 RU 的查詢，則每秒能夠發出 10 個這樣的查詢。 超出該輸送量的要求，其速率會受限，這時您就應該重試要求。 如果您使用用戶端驅動程式，其可支援自動重試邏輯。

您可以在資料庫或容器上佈建輸送量，每個策略都可以根據案例來協助您節省成本。

## <a name="optimize-by-provisioning-throughput-at-different-levels"></a>在不同層級佈建輸送量以便最佳化

* 如果您在資料庫上佈建輸送量，所有容器 (例如，該資料庫內的集合/資料表/圖表) 都可以根據負載來共用輸送量。 根據一組特定容器上的工作負載，在資料庫層級保留的輸送量不會平均地共用。

* 如果您在容器上佈建輸送量，系統會根據 SLA 來保證該容器的輸送量。 如果您要將負載平均分配給容器的所有邏輯分割區，邏輯分割區索引鍵的選擇就十分重要。 如需詳細資訊，請參閱[資料分割](partitioning-overview.md)和[水平調整](partitioning-overview.md)文章。

用來決定佈建輸送量策略的一些指導方針如下：

**考慮在下列情況中，在包含一組容器的 Azure Cosmos 資料庫上布建輸送量 () **：

1. 您有幾十個 Azure Cosmos 容器，並想要在全部或其中一部分共用輸送量。 

2. 您要從設計目的是要在 IaaS 所裝載的 VM 上或在內部部署環境上執行的單一租用戶資料庫 (例如，NoSQL 或關聯式資料庫) 遷移至 Azure Cosmos DB。 而且，如果您有許多集合/資料表/圖形，而且您不想對資料模型進行任何變更。 請注意，如果您從內部部署資料庫進行遷移時，不會更新您的資料模型，您可能必須危及 Azure Cosmos DB 所提供的一些優點。 建議您一律重新存取資料模型，以獲得最佳效能，並獲得最佳成本。 

3. 您想要在會有非預期突增工作負載的資料庫層級，透過集區輸送量來應付不在規劃中的突增工作負載。 

4. 您不想對個別容器設定特定輸送量，而是想要跨資料庫內的一組容器取得彙總輸送量。

**如果您有下列情況，請考慮對個別容器佈建輸送量：**

1. 您有一些 Azure Cosmos 容器。 由於 Azure Cosmos DB 不限結構描述，所以容器所含的項目可具有異質結構描述，而且客戶不必建立多個容器類型 (每個實體一種)。 如果應該將個別容器 (假設有 10 到 20 個) 群組為單一容器，您永遠應該考慮此選項。 容器至少有 400 RU 時，將 10 到 20 個容器形成一個容器集區會更符合成本效益。 

2. 您想要控制特定容器的輸送量，並根據 SLA 在指定容器上獲得保證的輸送量。

**考慮混合使用上述兩種策略：**

1. 如先前所述，Azure Cosmos DB 可讓您混合使用上述兩種策略，因此您現在可以在 Azure Cosmos 資料庫內有某些可能會共用資料庫上所佈建輸送量的容器，以及在相同資料庫內有某些可能會有專用佈建輸送量的容器。 

2. 您可以套用上述策略來形成混合式組態，以便同時擁有這兩個資料庫層級的佈建輸送量，且其中的某些容器擁有專用的輸送量。

如下表所示，您可以根據所選擇的 API，在不同的資料粒度佈建輸送量。

|API|針對**共用**輸送量，請設定 |針對**專用**輸送量，請設定 |
|----|----|----|
|SQL API|資料庫|容器|
|適用於 MongoDB 的 Azure Cosmos DB API|資料庫|集合|
|Cassandra API|keyspace|Table|
|Gremlin API|資料庫帳戶|圖形|
|資料表 API|資料庫帳戶|Table|

您可以在不同層級佈建輸送量，以根據工作負載特性獲得最佳成本。 如先前所述，您可以隨時以程式設計方式，對個別容器或集體形式的一組容器增加或減少所佈建的輸送量。 隨著工作負載的變化來彈性調整輸送量，您便只需支付您已設定的輸送量。 如果您的容器或一組容器分散在多個區域，則您對一個容器或一組容器所設定的輸送量，保證可供所有區域使用。

## <a name="optimize-with-rate-limiting-your-requests"></a>透過限制要求的速率來進行最佳化

對於不太受延遲影響的工作負載，您可以佈建少一點輸送量，並讓應用程式在實際輸送量超過所佈建的輸送量時，處理限速工作。 伺服器會事先 `RequestRateTooLarge` (HTTP 狀態碼) 429 結束要求，並傳回 `x-ms-retry-after-ms` 標頭，指出使用者在重試要求之前必須等待的時間長度（以毫秒為單位）。 

```html
HTTP Status 429, 
 Status Line: RequestRateTooLarge 
 x-ms-retry-after-ms :100
```

### <a name="retry-logic-in-sdks"></a>SDK 中的重試邏輯 

原生 SDK (.NET/.NET Core、Java、Node.js 和 Python) 會隱含地攔截這個回應、採用伺服器指定的 retry-after 標頭，然後重試此要求。 除非有多個用戶端同時存取您的帳戶，否則下次重試將會成功。

如果您有多個用戶端的累積作業高於要求速率，則預設的重試計數（目前設定為9）可能還不夠。 在這種情況下，用戶端會對 `RequestRateTooLargeException` 應用程式擲回狀態碼429。 在 ConnectionPolicy 執行個體上設定 `RetryOptions`，即可變更預設重試次數。 根據預設， `RequestRateTooLargeException` 如果要求繼續以要求速率以上的方式運作，則在30秒的累計等候時間之後，會傳回 with 狀態碼429。 即使目前的重試計數小於最大重試計數 (預設值 9 或使用者定義的值)，也會發生這種情況。 

[MaxRetryAttemptsOnThrottledRequests](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretryattemptsonthrottledrequests?preserve-view=true&view=azure-dotnet) 設定為3，因此在此情況下，如果要求作業的速率限制超過容器的保留輸送量，則在將例外狀況擲回應用程式之前，要求作業會重試三次。 [MaxRetryWaitTimeInSeconds](/dotnet/api/microsoft.azure.documents.client.retryoptions.maxretrywaittimeinseconds?preserve-view=true&view=azure-dotnet#Microsoft_Azure_Documents_Client_RetryOptions_MaxRetryWaitTimeInSeconds) 設定為60，因此在此情況下，如果第一個要求之後的累計重試等候時間（以秒為單位）超過60秒，則會擲回例外狀況。

```csharp
ConnectionPolicy connectionPolicy = new ConnectionPolicy(); 
connectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 3; 
connectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 60;
```

## <a name="partitioning-strategy-and-provisioned-throughput-costs"></a>資料分割策略和所佈建輸送量的成本

若要在 Azure Cosmos DB 中獲得最佳成本，一定要有良好的資料分割策略。 請確定分割區並未傾斜 (透過儲存體計量即可得知)。 請確定分割區的輸送量並未傾斜 (透過輸送量計量即可得知)。 請確定資料分割策略未向特定分割區索引鍵傾斜。 儲存體中的主索引鍵可透過計量來得知，但索引鍵取決於應用程式的存取模式。 請最好思考什麼才是正確的邏輯分割區索引鍵。 良好的分割區索引鍵應該會有下列特性：

* 選擇分割區索引鍵，將工作負載平均分散到所有分割區，並隨著時間平均分散。 換句話說，您不應該讓某些索引鍵有大多數的資料，某些索引鍵卻只有較少資料或沒有資料。 

* 所選擇的分割區索引鍵，必須能讓存取模式平均分散到所有邏輯分割區。 工作負載要合理地平均分給所有索引鍵。 換句話說，不能讓多數工作負載集中在少數的特定索引鍵。 

* 所選擇的分割區索引鍵，必須要有各種值。 

基本概念是將容器中的資料和活動分散到這組邏輯分割區，以便將適用於資料儲存體和輸送量的資源散發到邏輯分割區。 分割區索引鍵的候選項目可能包含經常在您的查詢中顯示為篩選的屬性。 可藉由在篩選述詞中包含分割區索引鍵，有效地路由傳送查詢。 透過這類資料分割策略，便可更輕鬆地將所佈建的輸送量最佳化。 

### <a name="design-smaller-items-for-higher-throughput"></a>設計較少量的項目以獲得較高輸送量 

指定作業的要求費用或要求處理成本，會與項目大小直接相關。 大型項目的作業成本高於較小型項目的作業成本。 

## <a name="data-access-patterns"></a>資料存取模式 

根據存取資料的頻率，以邏輯方式將資料分成各個邏輯類別，永遠是最好的做法。 藉由將資料分類為熱門、不冷不熱或冷門資料，您可以微調所取用的儲存體及所需的輸送量。 根據存取頻率，您可以將資料放入不同容器 (例如，資料表、圖表和集合)，並微調對容器所佈建的輸送量，以因應該區段資料的需求。 

此外，如果您使用 Azure Cosmos DB，而且您知道您不會以特定資料值進行搜尋，也不會經常存取這些值，則請以壓縮的方式儲存這些屬性的值。 運用這個方法後，您可以節省儲存體空間、索引空間和所佈建的輸送量，從而降低成本。

## <a name="optimize-by-changing-indexing-policy"></a>變更索引編製原則以便最佳化 

根據預設，Azure Cosmos DB 會自動地對每一筆記錄的每個屬性編製索引。 這是為了簡化開發，並確保各種不同類型的臨機操作查詢都能有絕佳的效能。 如果您的大型記錄有數千個屬性，支付輸送量成本來對每個屬性編製索引可能不是實用的方法，當您只是要針對其中的 10 個或 20 個屬性進行查詢時更是如此。 隨著您快要進行到處理特定工作負載的階段，我們的指導是您要微調索引原則。 如需 Azure Cosmos DB 索引編製原則的完整詳細資料，請至[此處](index-policy.md)尋找。 

## <a name="monitoring-provisioned-and-consumed-throughput"></a>監視佈建和取用的輸送量 

您可以監視所佈建的 RU 總數、限速要求的數目，以及您已在 Azure 入口網站中取用的 RU 數目。 下圖顯示使用量計量範例：

:::image type="content" source="./media/optimize-cost-throughput/monitoring.png" alt-text="在 Azure 入口網站中監視要求單位":::

您也可以設定警示，以確認限速要求的數目是否超過特定閾值。 如需詳細資訊，請參閱[如何監視 Azure Cosmos DB](use-metrics.md)一文。 這些警示可以傳送電子郵件給帳戶管理員，或呼叫自訂 HTTP Webhook 或 Azure 函式來自動增加佈建的輸送量。 

## <a name="scale-your-throughput-elastically-and-on-demand"></a>彈性地隨需調整輸送量 

您必須支付所佈建的輸送量，因此讓佈建的輸送量符合所需可協助您避免未用輸送量的費用。 您可以隨時視需要相應增加或減少所佈建的輸送量。 如果您的輸送量需求非常可預測，您可以使用 Azure Functions 並使用計時器觸發程式來 [增加或減少排程的輸送量](scale-on-schedule.md)。 

* 監視 RU 取用量和限速要求的比率，您可能會發現您不需要整天或整週都保有固定的佈建輸送量。 夜間或週末所收到的流量可能會較少。 藉由使用 Azure 入口網站或 Azure Cosmos DB 原生 SDK 或 REST API，您可以隨時調整所佈建的輸送量。 Azure Cosmos DB 的 REST API 會提供端點讓您以程式設計方式更新容器的效能層級，以便您可以簡單地根據當天時間或星期幾來從程式碼調整輸送量。 此作業在執行時不會造成停機，而且通常不到一分鐘即可生效。 

* 當您在資料移轉期間將資料擷取到 Azure Cosmos DB 時，就是該調整輸送量的時候。 完成移轉之後，您便可以將所佈建的輸送量相應減少到可以應付解決方案的穩定狀態。  

* 請記住，計費的最小單位是一個小時，因此如果您變更所佈建輸送量的頻率超過一小時一次，並不會節省任何費用。

## <a name="determine-the-throughput-needed-for-a-new-workload"></a>判斷新工作負載所需的輸送量 

若要判斷該為新工作負載佈建多少輸送量，您可以使用下列步驟： 

1. 使用容量規劃工具執行初始的粗略評估，並在 Azure 入口網站中透過 Azure Cosmos Explorer 的協助來調整您的預估值。 

2. 建議您建立輸送量高於預期值的容器，然後視需要相應減少。 

3. 建議您使用其中一個原生 Azure Cosmos DB SDK，以便在要求遭到限速時受惠於自動重試。 如果您工作所在的平台不受支援，而且您使用 Cosmos DB 的 REST API，則請使用 `x-ms-retry-after-ms` 標頭來實作您自己的重試原則。 

4. 請確定當所有重試都失敗時，您的應用程式程式碼可正常地支援這個情況。 

5. 您可以從 Azure 入口網站設定警示，以便收到限速通知。 一開始，您可以使用保守一點的限制 (例如，過去 15 分鐘 10 個限速要求)，然後在了解您實際的取用量後改採更積極的規則。 有時候，達到速率限制是不錯的訊號，因為這代表您充分利用完您所設定的限制數量，而那正是您的目標。 

6. 請使用監視功能來了解您的流量模式，以便您可以考慮是否需要動態調整您針對一整天或一週所佈建的輸送量。 

7. 請定期監視您已佈建與已取用的輸送量比率，以確定您所佈建的容器和資料庫未超過所需數目。 多佈建一點輸送量會是不錯的安全措施。  

### <a name="best-practices-to-optimize-provisioned-throughput"></a>獲得最佳佈建輸送量的最佳做法 

下列步驟可協助您在使用 Azure Cosmos DB 時，讓解決方案具有高度擴充性並符合成本效益。  

1. 如果您在容器和資料庫上明顯過度佈建輸送量，請檢閱已佈建的 RU 與已取用的 RU，然後微調工作負載。  

2. 若要估計您的應用程式所需的保留輸送量，其中一個方法為對照應用程式使用的代表性 Azure Cosmos 容器或資料庫，記錄與執行一般作業相關聯的要求單位 RU 費用，然後估計您預期每秒會執行的作業數目。 此外，請務必測量並包含一般查詢和其使用量。 若要了解如何以程式設計方式或使用入口網站來預估查詢的 RU 費用，請參閱[最佳化查詢成本](./optimize-cost-reads-writes.md)。 

3. 取得作業的另一種方式，也就是在 ru 中取得成本的方式，就是啟用 Azure 監視器記錄，讓您能夠細分作業/持續時間和要求費用。 Azure Cosmos DB 會提供每一項作業的要求費用，以便您可以從回應回存每個作業費用，然後用於分析。 

4. 您可以視需要彈性地相應增加和減少所佈建的輸送量，以因應工作負載需求。 

5. 您可以視需要新增和移除與 Azure Cosmos 帳戶相關聯的區域，並控制成本。 

6. 請確定您有將資料和工作負載平均分配到容器的所有邏輯分割區。 如果分割區分配不均，將會導致所佈建的輸送量比所需值還多。 如果您發現您的分配傾斜了，建議您重新將工作負載平均分配給所有分割區，或是將資料重新分割。 

7. 如果您有許多容器，而且這些容器不需要 SLA，則可以使用以資料庫為基礎的供應項目 (這個供應項目不適用每一容器輸送量 SLA)。 請識別您要將哪些 Azure Cosmos 容器遷移至資料庫層級輸送量供應項目，然後再使用變更摘要型解決方案來遷移這些容器。 

8. 請考慮使用「Cosmos DB 免費層」(一年免費)、試用 Cosmos DB (最多三個區域) 或可下載的 Cosmos DB 模擬器來用於開發/測試案例。 藉由使用這些選項進行測試開發，您可以大幅降低成本。  

9. 如果情況適用，您更可執行工作負載專屬的成本最佳化，例如，增加批次大小、負載平衡多個區域的讀取，以及刪除重複資料。

10. 運用 Azure Cosmos DB 的保留容量，您可以獲得為期三年最多 65% 的大幅折扣。 使用 Azure Cosmos DB 保留容量模型，即代表您承諾會為一段時間內所需的要求單位量預付費用。 折扣會分層提供，您在一段較長時間內所使用的要求單位量越多，折扣就會越高。 這些折扣會立即適用。 若使用的 RU 超過您佈建的值，則會根據非保留容量的成本來收費。 如需詳細資訊，請參閱 [Cosmos DB 保留容量](cosmos-db-reserved-capacity.md))。 您可以考慮購買保留容量，以進一步降低所佈建輸送量的成本。  

## <a name="next-steps"></a>後續步驟

接下來，您可以利用下列文章繼續深入了解 Azure Cosmos DB 中有關成本最佳化的詳細資訊：

* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解 [Azure Cosmos DB 帳單](understand-your-bill.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](./optimize-cost-reads-writes.md)
* 深入了解[最佳化多重區域 Azure Cosmos 帳戶的成本](optimize-cost-regions.md)
