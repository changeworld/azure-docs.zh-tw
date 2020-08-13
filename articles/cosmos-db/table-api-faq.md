---
title: 關於 Azure Cosmos DB 中資料表 API 的常見問題集
description: 取得 Azure Cosmos DB 中資料表 API 常見問題集的解答
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: sngun
ms.openlocfilehash: 65f276662ac4837003c7a7078b6197ba155eadc9
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167584"
---
# <a name="frequently-asked-questions-about-the-table-api-in-azure-cosmos-db"></a>關於 Azure Cosmos DB 中資料表 API 的常見問題集

您必須先註冊 Azure 訂用帳戶，才能在 [Azure 入口網站](https://portal.azure.com)中使用 Azure Cosmos DB 資料表 API。 註冊之後，您可以將 Azure Cosmos DB 資料表 API 帳戶加入 Azure 訂用帳戶，接著再將資料表加入帳戶。 您可以在 [Azure Cosmos DB 資料表 API 簡介](table-introduction.md)中找到支援的語言和相關快速入門。

## <a name="table-api-in-azure-cosmos-db-vs-azure-table-storage"></a><a id="table-api-vs-table-storage"></a>Azure Cosmos DB 與 Azure 資料表儲存體中的資料表 API

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>資料表 API 與 Azure 資料表儲存體的行為有哪些不同？

來自 Azure 資料表儲存體而想要使用 Azure Cosmos DB 資料表 API 來建立資料表的使用者，應該注意以下的部分行為差異：

* Azure Cosmos DB 資料表 API 使用保留容量模型來確保效能獲得保證，但這也表示，使用者一建立資料表，就必須支付其容量費用，即使該容量並未使用也是如此。 而在使用 Azure 資料表儲存體時，使用者只需要就使用的容量付費。 此一行為有助於解釋，為何資料表 API 可於第 99 個百分位數的水準提供 10 毫秒的讀取和 15 毫秒的寫入 SLA，而 Azure 資料表儲存體則是提供 10 秒的 SLA。 但也因為這樣，資料表 API 的資料表 (甚至是沒有任何要求的空白資料表) 必須付出金錢，才能確保有容量可用來處理其所收到的任何要求，並以 Azure Cosmos DB 所提供的 SLA 來進行。

* 資料表 API 所傳回的查詢結果，不會和 Azure 資料表儲存體一樣使用資料分割索引鍵/資料列索引鍵順序來排序。

* 資料列索引鍵最多只能是 255 個位元組。

* 批次最多只可有 2 MB。

* 目前不支援 CORS。

* 資料表名稱在 Azure 資料表儲存體中不區分大小寫，但在 Azure Cosmos DB 資料表 API 中則有區分。

* 部分 Azure Cosmos DB 用於編碼資訊 (例如二進位欄位) 的內部格式，目前不如預期地有效率。 因此這會對資料大小造成非預期的限制。 例如，目前無法使用資料表實體的完整 1 Meg 來儲存二進位資料，因為編碼方式增加了資料的大小。

* 目前不支援實體屬性名稱 'ID'。

* TableQuery TakeCount 不限於 1000。

* 就 REST API 來說，Azure Cosmos DB 資料表 API 並不支援某些端點/查詢選項：

  | REST 方法 | REST 端點/查詢選項 | 文件 URL | 說明 |
  | ------------| ------------- | ---------- | ----------- |
  | GET、PUT | `/?restype=service@comp=properties`| [設定資料表服務屬性](/rest/api/storageservices/set-table-service-properties)和[取得資料表服務屬性](/rest/api/storageservices/get-table-service-properties) | 此端點可用來設定 CORS 規則、儲存體分析組態以及記錄設定。 CORS 目前不受支援，而且分析和記錄在 Azure Cosmos DB 和 Azure 儲存體資料表中的處理方式不同 |
  | OPTIONS | `/<table-resource-name>` | [事前 CORS 資料表要求](/rest/api/storageservices/preflight-table-request) | 這是 CORS 的一部分，但目前不受 Azure Cosmos DB 支援。 |
  | GET | `/?restype=service@comp=stats` | [取得資料表服務統計資料](/rest/api/storageservices/get-table-service-stats) | 提供主要與次要資料庫之間的資料複寫速度有多快的資訊。 因為複寫是寫入的一部分，因此這不是 Cosmos DB 中的必須作業。 |
  | GET、PUT | `/mytable?comp=acl` | [取得資料表 ACL](/rest/api/storageservices/get-table-acl) 和[設定資料表 ACL](/rest/api/storageservices/set-table-acl) | 這會取得並設定用來管理共用存取簽章 (SAS) 的預存存取原則。 雖然 SAS 有受到支援，但其設定和管理方式並不相同。 |

* Azure Cosmos DB 資料表 API 只支援 JSON 格式，而不支援 ATOM。

* 雖然 Azure Cosmos DB 支援共用存取簽章 (SAS)，但它也有某些不支援的原則，具體地說，它不支援與管理作業有關的原則，例如用以建立新資料表的權限。

* .NET SDK 尤其如此，裡面會有一些 Azure Cosmos DB 目前不支援的類別和方法。

  | 類別 | 不支援的方法 |
  |-------|-------- |
  | CloudTableClient | \*ServiceProperties* |
  |                  | \*ServiceStats* |
  | CloudTable | SetPermissions* |
  |            | GetPermissions* |
  | TableServiceContext | * (此類別已被取代) |
  | TableServiceEntity | " " |
  | TableServiceExtensions | " " |
  | TableServiceQuery | " " |

## <a name="other-frequently-asked-questions"></a>其他常見問題

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>我需要新的 SDK，才能使用資料表 API 嗎？

不必，現有的儲存體 SDK 應該還是有效。 不過，建議您隨時取得最新的 SDK 以獲得最佳支援，以及在諸多情況下獲得更優異的效能。 請參閱 [Azure Cosmos DB 資料表 API 簡介](table-introduction.md)中的可用語言清單。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>我需要哪一個連接字串，才能連接資料表 API？

連接字串為：

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmosDB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

您可以從 Azure 入口網站的 [連接字串] 頁面取得連接字串。

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>如何在資料表 API 的 .NET SDK 中覆寫要求選項的組態設定？

有些設定是在 CreateCloudTableClient 方法上處理，有些則是透過用戶端應用程式中 appSettings 區段的 app.config 進行處理。 如需組態設定的相關資訊，請參閱 [Azure Cosmos DB 功能](tutorial-develop-table-dotnet.md)。

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>對使用現有「Azure 資料表」儲存體 SDK 的客戶來說，是否有任何變更？

無。 對使用現有「Azure 資料表」儲存體 SDK 的現有客戶或新客戶來說，並無任何變更。

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>如何檢視儲存在 Azure Cosmos DB 中的資料表 資料，以便與資料表 API 搭配使用？

您可以使用 Azure 入口網站來瀏覽資料。 您也可以使用資料表 API 程式碼或下一個回答所述的工具。

### <a name="which-tools-work-with-the-table-api"></a>哪些工具可與資料表 API 搭配使用？

您可以使用 [Azure 儲存體總管](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)。

能夠彈性地以先前指定之格式採用連接字串的工具，均可支援新的資料表 API。 [Azure 儲存體用戶端工具](../storage/common/storage-explorers.md)頁面上提供一份資料表工具清單。

### <a name="is-the-concurrency-on-operations-controlled"></a>可以在作業中控制並行存取嗎？

是，開放式並行存取會透過使用 Etag 機制來提供。

### <a name="is-the-odata-query-model-supported-for-entities"></a>是否針對實體支援 OData 查詢模型？

是，資料表 API 支援 OData 查詢和 LINQ 查詢。

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>我可以在同一個應用程式中並行連線至「Azure 資料表」儲存體和「Azure Cosmos DB 資料表 API」嗎？

是，您可以建立兩個不同的 CloudTableClient 執行個體，並透過連接字串讓它們分別指向自己的 URI，藉此進行連線。

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>如何將現有的 Azure 資料表儲存體應用程式移轉到這個供應項目？

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) 和 [Azure Cosmos DB 資料移轉工具](import-data.md)皆有受到支援。

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>假設我一開始就有 n GB 的資料，而我的資料將隨著時間過去成長為 1 TB，我該如何針對此服務完成儲存體大小的擴充？

Azure Cosmos DB 的設計乃藉由使用水平調整提供無限制的儲存體。 這項服務能監視並有效地增加您的儲存體。

### <a name="how-do-i-monitor-the-table-api-offering"></a>如何監視資料表 API 供應項目？

您可以使用資料表 API 的 [計量] 窗格，來監視要求和儲存體使用量。

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何計算我需要的輸送量？

您可以使用的容量估計工具來計算作業所需的 TableThroughput。 如需詳細資訊，請參閱[估算要求單位和資料儲存體 (英文)](https://www.documentdb.com/capacityplanner)。 通常，您可以將實體顯示為 JSON，並為您的作業提供編號。

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用資料表 API SDK 與模擬器嗎？

目前沒有。

### <a name="can-my-existing-application-work-with-the-table-api"></a>我現有的應用程式可以與資料表 API 搭配運作嗎？

是，可支援相同的 API。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>如果我不想要使用「資料表 API」功能，是否需要將現有的「Azure 資料表」儲存體應用程式移轉至 SDK？

否，您可以建立和使用現有的「Azure 資料表」儲存體資產，而不會有任何形式的服務中斷。 不過，如果您不使用資料表 API ，就無法從自動索引、額外的一致性選項或全域散發中獲益。

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>如何在「資料表 API」中新增跨多個 Azure 區域的資料複寫？

您可以使用 Azure Cosmos DB 入口網站的[全域複寫設定](tutorial-global-distribution-sql-api.md#portal)，加入適合應用程式的區域。 若要開發全域散發的應用程式，您還應該使用設為本地區域的 PreferredLocation 資訊來加入您的應用程式，以提供低讀取延遲。

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>如何在「資料表 API」中變更帳戶的主要寫入區域？

您可以使用 Azure Cosmos DB 的全球複寫入口網站窗格來加入一個區域，然後容錯移轉至所需的區域。 如需指示，請參閱[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>當我散發資料時，如何設定慣用的讀取區域以取得低延遲？

若要協助讀取本機位置，請使用 app.config 檔案中的 PreferredLocation 金鑰 。 對於現有的應用程式，如果您已設定 LocationMode，資料表 API 會擲回錯誤。 請移除該程式碼，因為資料表 API 會從 app.config 檔案取用這項資訊。 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>如何看待資料表 API 中的一致性層級？

Azure Cosmos DB 可在一致性、高可用性及延遲之間提供完全合乎邏輯的取捨。 Azure Cosmos DB 資料表 API 提供開發人員五種一致性層級，所以您可以在資料表層級選擇合適的一致性模型，以及在查詢資料時進行個別要求。 當用戶端連線時，它可以指定一致性層級。 您可以透過 CreateCloudTableClient 的 consistencyLevel 引數來變更層級。

資料表 API 會利用「讀取您自己的寫入」來提供低延遲的讀取，並採用限定過期一致性的預設值。 如需詳細資訊，請參閱[一致性層級](consistency-levels.md)。

根據預設，Azure 資料表儲存體提供區域內的強式一致性，以及次要位置中最終的一致性。

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>「Azure Cosmos DB 資料表 API」是否提供比「Azure 資料表」更多的一致性層級？

是，如需如何從 Azure Cosmos DB 分散式本質獲益的相關資訊，請參閱[一致性層級](consistency-levels.md)。 由於我們提供一致性層級保證，因此您可以放心使用。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>啟用全域散發時，複寫資料需要多久的時間？

Azure Cosmos DB 會在當地區域持久認可資料，並在幾毫秒內立即將資料推送到其他區域。 這項複寫操作只取決於資料中心的來回時間 (RTT)。 若要深入了解 Azure Cosmos DB 的全域散發能力，請參閱[透過 Azure Cosmos DB 進行全域資料散發](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>可以變更讀取要求一致性層級嗎？

有了 Azure Cosmos DB，您可以設定容器層級的一致性層級 (在資料表中)。 透過 .NET SDK，您可以在 app.config 檔案中提供 TableConsistencyLevel 索引鍵的值，藉以變更層級。 可能的值包括：「強式」、「限定過期」、「工作階段」、「一致前置詞」或「最終」。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的 Tunable 資料一致性層級](consistency-levels.md)。 主要概念在於，您無法將要求一致性層級設定為高於資料表設定的值。 例如，您無法將資料表的一致性層級設定為「最終」，而將要求一致性層級設定為「強式」。

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>當區域發生服務中斷時，「資料表 API」如何處理容錯移轉？

資料表 API 會利用 Azure Cosmos DB 的全域散發平台。 若要確保您的應用程式可以容許資料中心停機，請在 Azure Cosmos DB 入口網站中針對該帳戶至少啟用一個以上的區域，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。 您可以使用入口網站來設定區域的優先順序，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。 [最新的 .NET 用戶端 SDK](table-sdk-dotnet.md) 會自動連接，但其他 SDK 則不會。 也就是說，它可以偵測到區域已關閉，並自動容錯移轉至新的區域。

### <a name="is-the-table-api-enabled-for-backups"></a>是否已啟用「資料表 API」的備份功能？

是，資料表 API 會利用 Azure Cosmos DB 的平台來進行備份。 備份會自動執行。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 進行線上備份及還原](online-backup-and-restore.md)。

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>資料表 API 預設會為實體的所有屬性編製索引嗎？

是，根據預設，它會為實體的所有屬性編製索引。 如需詳細資訊，請參閱 [Azure Cosmos DB中的編製索引原則](index-policy.md)。

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>這表示我不需要建立多個索引來滿足查詢嗎？

是，Azure Cosmos DB 資料表 API 會為所有屬性提供自動編製索引，而不需任何結構描述定義。 這項資訊能讓開發人員專注於應用程式，而不需擔心索引的建立和管理。 如需詳細資訊，請參閱 [Azure Cosmos DB中的編製索引原則](index-policy.md)。

### <a name="can-i-change-the-indexing-policy"></a>我可以變更索引編制原則嗎？

是，您可以提供索引定義來變更索引編制原則。 您需要適當地進行編碼和逸出這些設定。

針對非 .NET SDK，索引編製原則只能在入口網站的**資料總管**中進行設定，請瀏覽至您想要變更的特定資料表，然後移至 [規模與設定] -> [索引編製原則]，進行所需的變更，然後 [儲存]。

透過 .NET SDK，您可在 app.config 檔案中提交原則：

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB 做為平台時似乎還有很多功能，例如排序、彙總、階層及其他功能。 你們會將這些功能加入資料表 API 嗎？

資料表 API 提供的查詢功能與 Azure 資料表儲存體相同。 Azure Cosmos DB 也支援排序、彙總、地理空間查詢、階層及各種不同的內建函式。 如需詳細資訊，請參閱 [SQL 查詢](how-to-sql-query.md)。

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>我何時應該變更資料表 API 的 TableThroughput？

滿足以下任一條件時，您應該變更 TableThroughput：

* 您即將要執行資料擷取、轉換及載入 (ETL)，或想要在短時間內上傳大量資料。
* 您需要更多來自後端容器或容器集的輸送量。 例如，您發現使用的輸送量比佈建的輸送量還多，而您正要開始進行節流。 如需詳細資訊，請參閱[設定 Azure Cosmos 容器輸送量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>我可以擴大或縮小資料表 API 資料表的輸送量嗎？

是，您可以使用 Azure Cosmos DB 入口網站的調整窗格來調整輸送量。 如需詳細資訊，請參閱[設定輸送量](set-throughput.md)。

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>新佈建的資料表是否有預設的 TableThroughput 設定？

是，如果您不透過 app.config 覆寫 TableThroughput，也不在 Azure Cosmos DB 中使用預先建立的容器，服務會建立輸送量為 400 的資料表。

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>對現有的「Azure 資料表」儲存體服務客戶來說，是否有任何定價變更？

無。 對現有的「Azure 資料表」儲存體客戶來說，並沒有任何價格上的變更。

### <a name="how-is-the-price-calculated-for-the-table-api"></a>如何針對資料表 API 計算價格？

價格取決於配置的 TableThroughput。

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>如何在資料表 API 供應項目中處理資料表上的任何速率限制？

如果要求速率超過為基礎容器或容器集佈建的輸送量容量，您就會收到錯誤，而 SDK 會套用重試原則來重試呼叫。

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>為何我需要選擇 PartitionKey 和 RowKey 以外的輸送量，才能運用 Azure Cosmos DB 的資料表 API 供應項目？

如果您未在 app.config 或透過入口網站提供輸送量，Azure Cosmos DB 將為您的容器設定預設輸送量。

Azure Cosmos DB 能提供效能和延遲上的保證，限定作業時的上限。 當引擎能針對租用戶作業強制執行控管時，這項保證是可行的。 設定 TableThroughput 能確保您享有保證的輸送量和延遲，因為平台會保留產能，保證作業成功。

透過輸送量規格，您可以彈性地變更它來從應用程式季節性當中獲得好處、滿足輸送量需求和節省成本。

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure 資料表儲存體對我而言並不貴，因為我只需要支付資料的儲存費用，而且我很少查詢。 即使我尚未執行單一交易或儲存任何項目，Azure Cosmos DB 資料表 API 供應項目似乎還是會向我收取費用。 您可以解釋嗎？

Azure Cosmos DB 已設計為全域散發的 SLA 型系統，並提供可用性、延遲和輸送量的保證。 當您在 Azure Cosmos DB 中保留輸送量時，與其他系統輸送量不同的是，它會提供保證。 Azure Cosmos DB 會提供客戶要求的其他功能，如次要索引和全域散發。

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>將資料內嵌至「Azure 資料表」儲存體時，我從未收到「配額已滿」通知 (表示分割區已滿)。 當我使用資料表 API 時，卻收到了這則訊息。 這個供應項目會限制我，並強迫我變更我現有的應用程式嗎？

Azure Cosmos DB 是 SLA 型系統，它提供無限制的調整和延遲、輸送量、可用性、一致性保證。 若要獲得保證的進階效能，您必須確定資料大小和索引是可管理且可調整的。 每個分割區索引鍵的實體或專案數目限制為 20 GB，以確保我們提供絕佳的查閱和查詢效能。 為了確保您的應用程式能夠針對 Azure 儲存體進行更佳的調整，建議您「避免」建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>這樣說來，資料表 API 仍然需要 PartitionKey 和 RowKey 嗎？

是。 因為「資料表 API」的介面區與「Azure 資料表」儲存體 SDK 的介面區類似，所以分割區索引鍵能提供有效率的資料散發方式。 資料列索引鍵在該資料分割內是唯一的。 資料列索引鍵必須存在且不能為 Null，如同在標準 SDK 中。 RowKey 的長度是 255 個位元組，而 PartitionKey 為 1 KB。

### <a name="what-are-the-error-messages-for-the-table-api"></a>資料表 API 的錯誤訊息將有哪些？

Azure 資料表儲存體和 Azure Cosmos DB 資料表 API 使用相同的 SDK，因此大部分的錯誤會相同。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>當我嘗試在資料表 API 中逐一建立許多資料表時，為什麼要進行節流？

Azure Cosmos DB 是 SLA 型系統，可提供延遲、輸送量、可用性及一致性的保證。 由於它是佈建的系統，因此會保留資源以保證這些需求。 系統會偵測到快速建立的資料表，並加以節流。 我們建議您查看資料表的建立速率，並降低為低於每分鐘 5 個。 請記住，資料表 API 是佈建的系統。 只要您一佈建，就必須開始支付費用。

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供關於 SDK 和 Bug 的意見反應？

您可以使用以下任何方式來分享意見反應：

* [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 最適合用於詢問程式設計問題。 確保您的問題[契合主題](https://stackoverflow.com/help/on-topic)且[盡可能提供愈多的詳細資料，讓問題更加清楚並可得到答覆](https://stackoverflow.com/help/how-to-ask)。

## <a name="next-steps"></a>後續步驟

* [使用 .NET SDK 與 Azure Cosmos DB 建置資料表 API 應用程式](create-table-dotnet.md)
* [建置 JAVA 應用程式來管理 Azure Cosmos DB 資料表 API 資料](create-table-java.md)