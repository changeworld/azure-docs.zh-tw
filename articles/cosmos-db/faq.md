---
title: 關於 Azure Cosmos DB 內不同 API 的常見問題集
description: 取得關於 Azure Cosmos DB (一種全域散發的多模型資料庫服務) 之常見問題集的解答。 了解產能、效能層級和調整。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 4bd29ce3bf2cc7cd69f86dbf172d3cd9a2044e79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570366"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>關於 Azure Cosmos DB 內不同 API 的常見問題集

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 有哪些一般使用案例？

對於新的 Web、行動裝置、遊戲和 IoT 應用程式，若其中自動調整規模、可預測的效能、毫秒回應時間的快速排序，以及查詢無結構描述之資料的能力都很重要，則 Azure Cosmos DB 是個不錯的選擇。 Azure Cosmos DB 適合用來進行快速開發，且支援應用程式資料模型的持續反覆運算。 管理使用者產生之內容和資料的應用程式，就是 [Azure Cosmos DB 的常見使用案例](use-cases.md)。

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可預測的效能？

[要求單位](request-units.md) (RU) 是 Azure Cosmos DB 的輸送量計算單位。 1 RU 的輸送量會對應至 1 KB 文件的 GET 輸送量。 根據完成作業所需的輸送量，Azure Cosmos DB 中的每個作業 (包括讀取、寫入、SQL 查詢和預存程序執行) 具有決定性的 RU 值。 您可以就單一 RU 計量來思考，而不是思考 CPU、IO 和記憶體以及它們分別如何影響您的應用程式輸送量。

您可以按照每秒輸送量 RU 的佈建輸送量來設定每個 Azure Cosmos 容器。 對於任何規模的應用程式，您可以將個別要求設為基準以測量其 RU 值，以及佈建容器來處理所有要求的要求單位總數。 您也可以隨著應用程式發展需求，擴大或縮小容器的輸送量。 如需有關要求單位的詳細資訊，以及用於判斷容器需求的說明，請參閱[輸送量計算機](https://www.documentdb.com/capacityplanner) \(英文\)。

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支援各種資料模型，例如索引鍵/值、單欄式資料、文件和圖形？

索引鍵/值、單欄式資料、文件和圖形都是原生支援的模型，因為 Azure Cosmos DB 內建 ARS (原子、記錄、序列) 設計。 原子、記錄、序列可以輕易地對應並且投射至各種資料模型。 目前已有適用於各種模型子集合的 API (SQL、MongoDB、資料表、Gremlin)，未來會有更多用於其他特定資料模型的 API。

Azure Cosmos DB 不需要結構描述的索引引擎能夠自動建立所內嵌之資料的索引，且不需要查詢任何結構描述或開發者的次要索引。 引擎依賴一組邏輯索引配置 (反向式、單欄式、樹狀目錄)，可將儲存配從索引和查詢的處理子系統分離。 Cosmos DB 也可以擴充支援一些有線網路通訊協定和 API，並將它們有效地轉譯成核心資料模型 (1) 和邏輯索引配置，(2) 造就其原生支援多個資料模型的獨特能力。

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>我可以使用多個 API 來存取資料嗎？

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 當多模型意味著 Azure Cosmos DB 支援多個 API 和多個資料模型時，不同的 API 就會針對儲存體和有線網路通訊協定使用不同的資料格式。 例如，SQL 使用 JSON、MongoDB 使用 BSON、資料表使用 EDM、Cassandra 使用 CQL、Gremlin 使用 JSON 格式。 因此，建議使用相同的 API 來對指定帳戶進行所有資料存取。

除了可互通的 Gremlin 和 SQL API，每個 API 都會獨立運作。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 符合 HIPAA 規範嗎？

是，Azure Cosmos DB 符合 HIPAA 規範。 HIPAA 建立了使用、洩漏與保護個別辨識健全狀況資訊的需求。 如需詳細資訊，請參閱 [Microsoft 信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的儲存體限制有哪些？

對於容器可以儲存在 Azure Cosmos DB 中的資料總量，沒有任何限制。

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Azure Cosmos DB 的輸送量限制有哪些？

對於容器可以在 Azure Cosmos DB 中支援的總輸送量，沒有任何限制。 其主要概念在於，將您的工作負載大致平均分配在數量夠大的資料分割索引鍵之間。

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>直接和閘道連線模式是否會加密？

是，這兩種模式一律會完整加密。

### <a name="how-much-does-azure-cosmos-db-cost"></a>Azure Cosmos DB 的費用是多少？

如需詳細資料，請參閱 [Azure Cosmos DB 定價詳細資料](https://azure.microsoft.com/pricing/details/cosmos-db/)頁面。 Azure Cosmos DB 使用量費用取決於已佈建的容器數目、容器在線上的時數，以及每個容器的佈建輸送量。

### <a name="is-a-free-account-available"></a>有免費的帳戶嗎？

是，您可以免費註冊限時的帳戶，沒有承諾用量。 若要註冊，請造訪[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，或在[試用 Azure Cosmos DB 常見問題集](#try-cosmos-db)中深入了解。

如果您不熟悉 Azure，可以註冊 [Azure 免費帳戶](https://azure.microsoft.com/free/)，就能得到 30 天免費試用以及信用額度，讓您試用所有 Azure 服務。 如果您有 Visual Studio 訂用帳戶，也享有[免費的 Azure 信用額度](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)，可用於所有 Azure 服務。

您也可以免費使用 [Azure Cosmos DB 模擬器](local-emulator.md)在本機開發及測試應用程式，而不需建立 Azure 訂用帳戶。 如果您滿意應用程式在 Azure Cosmos DB 模擬器中的運作方式，就可以切換成使用雲端的 Azure Cosmos DB 帳戶。

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>如何取得 Azure Cosmos DB 的其他協助？

若要詢問技術問題，您可以將問題張貼到下列兩個問答論壇之一：

* [Microsoft 問與答頁面](https://docs.microsoft.com/answers/topics/azure-cosmos-db.html) \(英文\)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 最適合用於詢問程式設計問題。 確保您的問題[契合主題](https://stackoverflow.com/help/on-topic)且[盡可能提供愈多的詳細資料，讓問題更加清楚並可得到答覆](https://stackoverflow.com/help/how-to-ask)。

若要要求新功能，請在 [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) 上建立新的要求。

若要修正您的帳戶問題，請在 Azure 入口網站中提出[支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>試用 Azure Cosmos DB 訂用帳戶

您現在不需要訂用帳戶即可享用限時的 Azure Cosmos DB 體驗，不僅免費，也沒有任何承諾用量。 若要註冊試用 Azure Cosmos DB 訂用帳戶，請移至[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，並使用任意個人 Microsoft 帳戶 (MSA)。 此訂用帳戶與 [Azure 免費試用](https://azure.microsoft.com/free/)有所區別，可以搭配 Azure 免費試用或 Azure 付費訂用帳戶使用。

試用 Azure Cosmos DB 訂用帳戶會顯示在 Azure 入口網站中，與您的使用者識別碼相關聯的其他訂用帳戶旁。

下列條件適用於試用 Azure Cosmos DB 訂用帳戶：

* 可以為個人 Microsoft 帳戶 (MSA) 授與帳戶存取。 避免使用 Azure Active Directory (Azure AD) 屬於公司 Azure AD 租使用者的帳戶或帳戶，他們可能會有可能封鎖存取權的限制。
* SQL、Gremlin API 和資料表帳戶的每個訂用帳戶一個[輸送量佈建的容器](./set-throughput.md#set-throughput-on-a-container)。
* MongoDB 帳戶的每個訂用帳戶最多三個[輸送量佈建的集合](./set-throughput.md#set-throughput-on-a-container)。
* 每個訂用帳戶一個[輸送量佈建的資料庫](./set-throughput.md#set-throughput-on-a-database)。 輸送量佈建的資料庫可以包含任意數目的容器。
* 10 GB 儲存體容量。
* 全域複寫試用於下列 [Azure 區域](https://azure.microsoft.com/regions/)：美國中部、北歐和東南亞
* 在容器層級佈建時，輸送量上限為 5 K RU/秒。
* 在資料庫層級佈建時，輸送量上限為 20 K RU/秒。
* 訂用帳戶將於 30 天之後到期，可以擴充到最多總共 31 天。 到期後，即會刪除所含的資訊。
* 無法為試用 Azure Cosmos DB 帳戶建立 Azure 支援票證，不過，會以現有支援計劃為訂閱者提供支援。

## <a name="set-up-azure-cosmos-db"></a>設定 Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何註冊 Azure Cosmos DB？

Azure 入口網站中已提供 Azure Cosmos DB。 首先，請註冊 Azure 訂用帳戶。 當您註冊之後，就可以將 Azure Cosmos DB 帳戶新增至您的 Azure 訂用帳戶。

### <a name="what-is-a-primary-key"></a>什麼是主要金鑰？

主要金鑰是用來存取帳戶所有資源的安全性權杖。 擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。 當您散發主鍵時，請特別小心。 主要主鍵和次要主要金鑰位於[Azure 入口網站][azure-portal]的 [**金鑰**] 分頁。 如需金鑰的詳細資訊，請參閱 [檢視、複製和重新產生存取金鑰](manage-with-cli.md#list-account-keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>設定 PreferredLocations 的目標區域有哪些？

PreferredLocations 值可以設定為任何提供 Cosmos DB 的 Azure 區域。 如需可用區域的清單，請參閱 [Azure 區域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>當我透過 Azure 的資料中心在世界各地散發資料時，應注意哪些事項？

所有的 Azure 區域均提供 Azure Cosmos DB ，如同 [Azure 區域](https://azure.microsoft.com/regions/)頁面所指出。 由於它是核心服務，因此每個新資料中心都會有一個 Azure Cosmos DB。

設定區域時，請記住 Azure Cosmos DB 涉及主權和政府雲端。 也就是如果您在[主權區域](https://azure.microsoft.com/global-infrastructure/)中建立帳戶，便無法覆寫到該[主權區域](https://azure.microsoft.com/global-infrastructure/)之外的位置。 同樣地，您也無法從外部帳戶覆寫到其他主權位置。

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>是否可以從容器層級輸送量佈建，切換成資料庫層級輸送量佈建？ 或者反過來？

容器和資料庫層級輸送量佈建是不同的供應項目，在其間切換需要將資料從來源移轉到目的地。 這表示您需要建立新資料庫或新容器，然後藉由使用[大量執行工具程式庫](bulk-executor-overview.md)或 [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) 來移轉資料。

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB 是否支援時間序列分析？

是的，Azure CosmosDB 支援時間序列分析，請參考這裡的[時間序列模式範例](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)。 此範例會顯示如何使用變更摘要來建置經過一段時間的資料彙總檢視。 您可以使用 Spark 串流或其他串流資料處理器來延伸方法的使用範圍。

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Azure Cosmos DB 服務配額和輸送量限制為何

如需詳細資訊，請參閱 Azure Cosmos DB [服務配額](concepts-limits.md)及[每個容器和資料庫的輸送量限制](set-throughput.md#comparison-of-models)文章。

## <a name="frequently-asked-questions-about-sql-api"></a><a id="sql-api-faq"></a>關於 SQL API 的常見問題集

### <a name="how-do-i-start-developing-against-the-sql-api"></a>如何開始針對 SQL API 進行開發？

首先，您必須註冊 Azure 訂用帳戶。 註冊 Azure 訂用帳戶之後，您可以將 SQL API 容器新增至您的 Azure 訂用帳戶。 如需新增 Azure Cosmos DB 帳戶的相關指示，請參閱[建立 Azure Cosmos 資料庫帳戶](create-sql-api-dotnet.md#create-account)。

[SDK](sql-api-sdk-dotnet.md) 適用於 .NET、Python、Node.js、JavaScript 和 Java。 開發人員也可以使用 [RESTful HTTP API](/rest/api/cosmos-db/)，經由各種平台和語言來與 Azure Cosmos DB 資源互動。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>我可以存取一些現有的範例作為有利的開端嗎？

GitHub 上提供 SQL API [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md) 和 [Python](sql-api-python-samples.md) SDK 的範例。

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API 資料庫支援無結構描述的資料嗎？

是，SQL API 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。 透過 Azure Cosmos DB SQL 查詢介面，就可立即查詢資料。

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 支援 ACID 交易嗎？

是，SQL API 支援以 JavaScript 預存程序和觸發程序表示的跨文件交易。 交易以每個容器的單一分割為範圍，且以 ACID 語意執行，也就是「全有或全無」，與其他並行執行的程式碼和使用者要求隔離。 如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。 

### <a name="what-is-a-container"></a>容器是什麼？

容器是一組文件及其相關聯的 JavaScript 應用程式邏輯。 容器是計費實體，其[成本](performance-levels.md)是由輸送量和使用的儲存體所決定。 容器可以跨越一或多個磁碟分割或伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

* 針對 SQL API，資源稱為容器。
* 針對適用於 MongoDB 帳戶的 Cosmos DB API，容器會對應至集合。
* 針對 Cassandra 和資料表 API 帳戶，容器會對應至資料表。
* 針對 Gremlin API 帳戶，容器會對應至圖表。

容器也是 Azure Cosmos DB 的計費實體。 每個容器根據佈建的輸送量和使用的儲存體空間，以小時為單位計費。 如需詳細資訊，請參閱 [Azure Cosmos DB 價格](https://azure.microsoft.com/pricing/details/cosmos-db/)。

### <a name="how-do-i-create-a-database"></a>我如何建立資料庫？

您可以使用 [Azure 入口網站](https://portal.azure.com) (如[新增容器](create-sql-api-java.md#add-a-container)所述)、其中一個 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 來建立資料庫。

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何設定使用者和權限？

您可以使用其中一個 [Cosmos DB API SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 來建立使用者和權限。

### <a name="does-the-sql-api-support-sql"></a>SQL API 支援 SQL 嗎？

SQL API 帳戶所支援的 SQL 查詢語言是 SQL Server 所支援的查詢功能增強子集。 Azure Cosmos DB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函式 (UDF) 支援擴充性。 JSON 文法允許將 JSON 文件模型化為節點加上標籤的樹狀結構，這是由 Azure Cosmos DB 的自動編製索引技術及 Azure Cosmos DB 的 SQL 查詢方言所使用。 如需使用 SQL 文法的詳細資訊，請參閱 [SQL 查詢][query]一文。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API 支援 SQL 彙總函式嗎？

SQL API 透過 SQL 文法，藉由彙總函式 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 來支援任何規模的低延遲彙總。 如需詳細資訊，請參閱[彙總函式](sql-query-aggregates.md)。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 如何提供並行存取？

SQL API 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。 每個 SQL API 資源都有一個 ETag，並且會在每次更新文件時，於伺服器上設定此 ETag。 ETag 標頭和目前的值會包含於所有回應訊息中。 Etag 可與 If-Match 標頭搭配使用，讓伺服器能夠決定是否應該更新資源。 If-Match 值是要經過檢查的 ETag 值。 如果 ETag 值符合伺服器的 ETag 值，就會更新資源。 如果 ETag 不再是最新狀態，則伺服器會拒絕該作業，並提供「HTTP 412 前置條件失敗」回應碼。 用戶端接著會重新擷取資源，以取得該資源目前的 ETag 值。 此外，ETag 可以與 If-None-Match 標頭搭配使用，來判斷是否需要重新擷取資源。

若要在 .NET 中使用開放式並行存取，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 類別。 如需 .NET 範例，請參閱 GitHub 上 DocumentManagement 範例中的 [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)。

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>我如何在 SQL API 中執行交易？

SQL API 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。 指令碼內的所有資料庫作業都會在快照隔離的情況下執行。 如果其為單一分割區容器，執行範圍就會限制為該容器。 如果容器已經過分割，則執行範圍會限制為該容器內具有相同分割區索引鍵值的文件。 文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。 如果 JavaScript 擲回錯誤，則會回復交易。 如需詳細資訊，請參閱 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](stored-procedures-triggers-udfs.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何將大量文件插入 Cosmos DB？

您可以利用下列任一方式將大量文件插入 Azure Cosmos DB：

* 大量執行程式工具，如[使用大量執行程式工具 .NET 程式庫](bulk-executor-dot-net.md)和[使用大量執行程式工具 Java 程式庫](bulk-executor-java.md)中所述
* 資料移轉工具，如 [Azure Cosmos DB 的資料庫移轉工具](import-data.md)中所述。
* 預存程序，如 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](stored-procedures-triggers-udfs.md)中所述。

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API 支援資源連結快取嗎？

是，因為 Azure Cosmos DB 是一項 RESTful 服務，資源連結是固定不變且可快取的。 SQL API 用戶端可以指定 "If-None-Match" 標頭來讀取任何類似資源的文件或容器，然後在伺服器版本變更之後更新其本機複本。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API 的本機執行個體可供使用嗎？

是。 [Azure Cosmos DB 模擬器](local-emulator.md)提供 Cosmos DB 服務的高逼真度模擬。 它支援與 Azure Cosmos DB 完全相同的功能，包括支援建立和查詢 JSON 文件、佈建和擴充集合，以及執行預存程序和觸發程序。 您可以使用 Azure Cosmos DB 模擬器來開發及測試應用程式，並且只需對 Azure Cosmos DB 的連接端點進行單一組態變更，就能將它們部署至全球規模的 Azure。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>在入口網站中從資料總管檢視文件中的長浮點值時，這些值為何會四捨五入。

這是 JavaScript 的限制。 JavaScript 會使用 IEEE 754 中指定的雙精確度浮點數格式數值，而且只能安全地保存 -(2<sup>53</sup> - 1) 與 2<sup>53</sup>-1 (即 9007199254740991) 之間的數值。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>物件階層中允許哪些權限？

您可以在容器層級和其子系 (例如文件、附件) 使用 ResourceTokens 來建立權限。 這意味著目前不允許在資料庫或帳戶層級嘗試建立權限。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>後續步驟

若要了解其他 API 中的常見問題集，請參閱：

* 關於[適用於 MongoDB 的 Azure Cosmos DB API](mongodb-api-faq.md) 的常見問題集
* 關於 [Azure Cosmos DB 中 Gremlin API](gremlin-api-faq.md) 的常見問題集
* 關於 [Azure Cosmos DB 中 Cassandra API](cassandra-faq.md) 的常見問題集
* 關於 [Azure Cosmos DB 中資料表 API](table-api-faq.md) 的常見問題集
