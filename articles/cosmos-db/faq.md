---
title: 關於 Azure 宇宙 DB 中不同 API 的常見問題
description: 取得關於 Azure Cosmos DB (一種全域散發的多模型資料庫服務) 之常見問題集的解答。 了解產能、效能層級和調整。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 25b669493e1ee32df4a8c6fc33bdb1b21b08c70f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683339"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>關於 Azure Cosmos DB 內不同 API 的常見問題集

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Azure Cosmos DB 有哪些一般使用案例？

對於新的 Web、行動裝置、遊戲和 IoT 應用程式，若其中自動調整規模、可預測的效能、毫秒回應時間的快速排序，以及查詢無結構描述之資料的能力都很重要，則 Azure Cosmos DB 是個不錯的選擇。 Azure Cosmos DB 適合用來進行快速開發，且支援應用程式資料模型的持續反覆運算。 管理使用者產生之內容和資料的應用程式，就是 [Azure Cosmos DB 的常見使用案例](use-cases.md)。

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Azure Cosmos DB 如何提供可預測的效能？

[要求單位](request-units.md) (RU) 是 Azure Cosmos DB 的輸送量計算單位。 1RU 輸送量對應於 1 KB 文件的 GET 輸送量。 根據完成作業所需的輸送量，Azure Cosmos DB 中的每個作業 (包括讀取、寫入、SQL 查詢和預存程序執行) 具有決定性的 RU 值。 您可以就單一 RU 計量來思考，而不是思考 CPU、IO 和記憶體以及它們分別如何影響您的應用程式輸送量。

您可以配置每個 Azure Cosmos 容器,其預配輸送量為每秒輸送量的 R, 對於任何規模的應用程式，您可以將個別要求設為基準以測量其 RU 值，以及佈建容器來處理所有要求的要求單位總數。 您也可以隨著應用程式發展需求，相應增加或減少容器的輸送量。 有關請求單位的詳細資訊以及確定容器需求的説明,請嘗試[吞吐量計算機](https://www.documentdb.com/capacityplanner)。

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Azure Cosmos DB 如何支援各種資料模型，例如索引鍵/值、單欄式資料、文件和圖形？

索引鍵/值、單欄式資料、文件和圖形都是原生支援的模型，因為 Azure Cosmos DB 內建 ARS (原子、記錄、序列) 設計。 原子、記錄、序列可以輕易地對應並且投射至各種資料模型。 目前已有適用於各種模型子集合的 API (SQL、MongoDB、資料表、Gremlin)，未來會有更多用於其他特定資料模型的 API。

Azure Cosmos DB 不需要結構描述的索引引擎能夠自動建立所內嵌之資料的索引，且不需要查詢任何結構描述或開發者的次要索引。 引擎依賴一組邏輯索引配置 (反向式、單欄式、樹狀目錄)，可將儲存配從索引和查詢的處理子系統分離。 Cosmos DB 也可以擴充支援一些有線網路通訊協定和 API，並將它們有效地轉譯成核心資料模型 (1) 和邏輯索引配置，(2) 造就其原生支援多個資料模型的獨特能力。

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>我可以使用多個 API 來訪問我的數據嗎?

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 當多模型意味著 Azure Cosmos DB 支援多個 API 和多個數據模型時,不同的 API 使用不同的數據格式進行存儲和有線協定。 例如,SQL使用JSON,蒙哥DB使用BSON,表使用EDM,卡桑德拉使用CQL,格雷姆林使用JSON格式。 因此,我們建議對給定帳戶中的資料的所有訪問使用相同的 API。

每個 API 獨立運行,但 Gremlin 和 SQL API 除外,它們是可互通的。

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Azure Cosmos DB 符合 HIPAA 規範嗎？

是，Azure Cosmos DB 符合 HIPAA 規範。 HIPAA 建立了使用、洩漏與保護個別辨識健全狀況資訊的需求。 有關詳細資訊,請參閱 Microsoft[信任中心](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA)。

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

* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [堆疊溢出](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 最適合用於詢問程式設計問題。 確保您的問題[契合主題](https://stackoverflow.com/help/on-topic)且[盡可能提供愈多的詳細資料，讓問題更加清楚並可得到答覆](https://stackoverflow.com/help/how-to-ask)。

若要要求新功能，請在 [User Voice](https://feedback.azure.com/forums/263030-azure-cosmos-db) 上建立新的要求。

若要修正您的帳戶問題，請在 Azure 入口網站中提出[支援要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>試用 Azure Cosmos DB 訂用帳戶

您現在不需要訂用帳戶即可享用限時的 Azure Cosmos DB 體驗，不僅免費，也沒有任何承諾用量。 要註冊嘗試 Azure 宇宙資料庫訂閱,請免費轉到[Azure 宇宙資料庫](https://azure.microsoft.com/try/cosmosdb/),並使用任何個人 Microsoft 帳戶 (MSA)。 此訂用帳戶與 [Azure 免費試用](https://azure.microsoft.com/free/)有所區別，可以搭配 Azure 免費試用或 Azure 付費訂用帳戶使用。

試用 Azure Cosmos DB 訂用帳戶會顯示在 Azure 入口網站中，與您的使用者識別碼相關聯的其他訂用帳戶旁。

下列條件適用於試用 Azure Cosmos DB 訂用帳戶：

* 可以授予個人 Microsoft 帳戶 (MSA) 的帳戶訪問許可權。 避免使用活動目錄 (AAD) 帳戶或屬於公司 AAD 租戶的帳戶,它們可能有限制,可能會阻止訪問授予。
* SQL、Gremlin API 與表帳戶每個訂閱的一個[輸送量預配容器](./set-throughput.md#set-throughput-on-a-container)。
* MongoDB 帳號每個訂閱最多提供三個[輸送量預配集合](./set-throughput.md#set-throughput-on-a-container)。
* 每個訂閱[的輸送量預配資料庫](./set-throughput.md#set-throughput-on-a-database)。 輸送量預配資料庫可以包含任意數量的容器。
* 10 GB 儲存體容量。
* 全域複製在以下 Azure[區域](https://azure.microsoft.com/regions/)可用:美國中部、北歐和東南亞
* 在容器級別預配時,最大輸送量為 5 K RU/s。
* 在資料庫級別預配時,最大輸送量為 20 K RU/s。
* 訂閱將在 30 天后過期,最多可延長至 31 天。
* 無法為試用 Azure Cosmos DB 帳戶建立 Azure 支援票證，不過，會以現有支援計劃為訂閱者提供支援。

## <a name="set-up-azure-cosmos-db"></a>設定 Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>如何註冊 Azure Cosmos DB？

Azure 入口網站中已提供 Azure Cosmos DB。 首先，請註冊 Azure 訂用帳戶。 註冊后,可以將 Azure Cosmos DB 帳戶添加到 Azure 訂閱中。

### <a name="what-is-a-master-key"></a>什麼是主要金鑰？

主要金鑰是可存取帳戶的所有資源的安全性權杖。 擁有此金鑰的人，對資料庫帳戶中的所有資源具有讀取和寫入存取權。 分配主要金鑰時，務必謹慎。 [Azure 入口網站][azure-portal]的 [金鑰]**** 刀鋒視窗提供主要金鑰和次要金鑰。 如需金鑰的詳細資訊，請參閱 [檢視、複製和重新產生存取金鑰](manage-with-cli.md#list-account-keys)。

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>設定 PreferredLocations 的目標區域有哪些？

PreferredLocations 值可以設定為任何提供 Cosmos DB 的 Azure 區域。 關於可用區域的清單,請參閱[Azure 區域](https://azure.microsoft.com/regions/)。

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>當我透過 Azure 的資料中心在世界各地散發資料時，應注意哪些事項？

所有的 Azure 區域均提供 Azure Cosmos DB ，如同 [Azure 區域](https://azure.microsoft.com/regions/)頁面所指出。 由於它是核心服務，因此每個新資料中心都會有一個 Azure Cosmos DB。

設定區域時，請記住 Azure Cosmos DB 涉及主權和政府雲端。 也就是如果您在[主權區域](https://azure.microsoft.com/global-infrastructure/)中建立帳戶，便無法覆寫到該[主權區域](https://azure.microsoft.com/global-infrastructure/)之外的位置。 同樣地，您也無法從外部帳戶覆寫到其他主權位置。

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>是否可以從容器層級輸送量佈建，切換成資料庫層級輸送量佈建？ 或者反過來？

容器和資料庫層級輸送量佈建是不同的供應項目，在其間切換需要將資料從來源移轉到目的地。 這意味著您需要建立新資料庫或新容器,然後使用[批次管理者庫](bulk-executor-overview.md)或[Azure 資料工廠移轉資料](../data-factory/connector-azure-cosmos-db.md)。

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure CosmosDB 是否支援時間序列分析？

是的，Azure CosmosDB 支援時間序列分析，請參考這裡的[時間序列模式範例](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns)。 此範例會顯示如何使用變更摘要來建置經過一段時間的資料彙總檢視。 您可以使用 Spark 串流或其他串流資料處理器來延伸方法的使用範圍。

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>什麼是 Azure 宇宙資料庫服務配額和輸送量限制

有關詳細資訊,請參閱 Azure Cosmos DB[服務配額](concepts-limits.md)以及[每個容器和資料庫文章的整個限制](set-throughput.md#comparison-of-models)。

## <a name="sql-api"></a>SQL API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>如何開始針對 SQL API 進行開發？

首先，您必須註冊 Azure 訂用帳戶。 註冊 Azure 訂用帳戶之後，您可以將 SQL API 容器新增至您的 Azure 訂用帳戶。 有關添加 Azure Cosmos 資料庫帳戶的說明,請參閱[建立 Azure Cosmos 資料庫帳戶](create-sql-api-dotnet.md#create-account)。

[SDK](sql-api-sdk-dotnet.md) 適用於 .NET、Python、Node.js、JavaScript 和 Java。 開發人員也可以使用 [RESTful HTTP API](/rest/api/cosmos-db/)，經由各種平台和語言來與 Azure Cosmos DB 資源互動。

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>我可以存取一些現有的範例作為有利的開端嗎？

GitHub 上提供 SQL API [.NET](sql-api-dotnet-samples.md)、[Java](https://github.com/Azure/azure-documentdb-java)、[Node.js](sql-api-nodejs-samples.md) 和 [Python](sql-api-python-samples.md) SDK 的範例。

### <a name="does-the-sql-api-database-support-schema-free-data"></a>SQL API 資料庫支援無結構描述的資料嗎？

是，SQL API 可讓應用程式儲存任意的 JSON 文件，而不需要結構描述定義或提示。 透過 Azure Cosmos DB SQL 查詢介面，就可立即查詢資料。

### <a name="does-the-sql-api-support-acid-transactions"></a>SQL API 支援 ACID 交易嗎？

是，SQL API 支援以 JavaScript 預存程序和觸發程序表示的跨文件交易。 交易以每個容器的單一分割為範圍，且以 ACID 語意執行，也就是「全有或全無」，與其他並行執行的程式碼和使用者要求隔離。 如果伺服器端執行 JavaScript 應用程式碼期間擲回例外狀況，則會回復整個交易。 

### <a name="what-is-a-container"></a>容器是什麼？

容器是一組文件及其相關聯的 JavaScript 應用程式邏輯。 容器是計費實體，其[成本](performance-levels.md)是由輸送量和使用的儲存體所決定。 容器可以跨越一或多個磁碟分割或伺服器，也可以進行調整以處理幾乎無限量的儲存體或輸送量。

* 對於 SQL API,容器映射到容器。
* 對於Cosmos DB的MongoDB帳戶的API,容器映射到集合。
* 針對 Cassandra 和資料表 API 帳戶，容器會對應至資料表。
* 針對 Gremlin API 帳戶，容器會對應至圖表。

容器也是 Azure Cosmos DB 的計費實體。 每個容器根據佈建的輸送量和使用的儲存體空間，以小時為單位計費。 如需詳細資訊，請參閱 [Azure Cosmos DB 價格](https://azure.microsoft.com/pricing/details/cosmos-db/)。

### <a name="how-do-i-create-a-database"></a>我如何建立資料庫？

可以使用[Azure 門戶](https://portal.azure.com)創建資料庫,如[添加容器](create-sql-api-java.md#add-a-container)[、Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)之一或 REST [API](/rest/api/cosmos-db/)中所述。

### <a name="how-do-i-set-up-users-and-permissions"></a>我如何設定使用者和權限？

您可以使用其中一個 [Cosmos DB API SDK](sql-api-sdk-dotnet.md) 或 [REST API](/rest/api/cosmos-db/) 來建立使用者和權限。

### <a name="does-the-sql-api-support-sql"></a>SQL API 支援 SQL 嗎？

SQL API 帳戶所支援的 SQL 查詢語言是 SQL Server 所支援的查詢功能增強子集。 Azure Cosmos DB 的 SQL 查詢語言提供豐富的階層式和關聯式運算子，且透過 JavaScript 型使用者定義函式 (UDF) 支援擴充性。 JSON 文法允許將 JSON 文件模型化為節點加上標籤的樹狀結構，這是由 Azure Cosmos DB 的自動編製索引技術及 Azure Cosmos DB 的 SQL 查詢方言所使用。 如需使用 SQL 文法的詳細資訊，請參閱 [SQL 查詢][query]一文。

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL API 支援 SQL 彙總函式嗎？

SQL API 透過 SQL 文法，藉由彙總函式 `COUNT`、`MIN`、`MAX`、`AVG` 和 `SUM` 來支援任何規模的低延遲彙總。 如需詳細資訊，請參閱[彙總函式](sql-query-aggregates.md)。

### <a name="how-does-the-sql-api-provide-concurrency"></a>SQL API 如何提供並行存取？

SQL API 透過 HTTP 實體標記或 ETag，支援開放式並行存取控制 (OCC)。 每個 SQL API 資源都有一個 ETag，並且會在每次更新文件時，於伺服器上設定此 ETag。 ETag 標頭和目前的值會包含於所有回應訊息中。 Etag 可與 If-Match 標頭搭配使用，讓伺服器能夠決定是否應該更新資源。 If-Match 值是要經過檢查的 ETag 值。 如果 ETag 值符合伺服器的 ETag 值，就會更新資源。 如果 ETag 不再是最新狀態，則伺服器會拒絕該作業，並提供「HTTP 412 前置條件失敗」回應碼。 用戶端接著會重新擷取資源，以取得該資源目前的 ETag 值。 此外，ETag 可以與 If-None-Match 標頭搭配使用，來判斷是否需要重新擷取資源。

若要在 .NET 中使用開放式並行存取，可使用 [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) 類別。 有關 .NET 示例,請參閱 GitHub 上的文檔管理範例中的[Program.cs。](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs)

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>我如何在 SQL API 中執行交易？

SQL API 透過 JavaScript 預存程序和觸發程序，支援語言整合式交易。 指令碼內的所有資料庫作業都會在快照隔離的情況下執行。 如果它是單分區容器,則執行將限定到該容器。 如果容器分區,則執行範圍將限定為容器中具有相同分區鍵值的文檔。 文件版本 (ETag) 的快照是在交易開始時取得，且只有當指令碼成功執行時才會認可。 如果 JavaScript 擲回錯誤，則會回復交易。 如需詳細資訊，請參閱 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](stored-procedures-triggers-udfs.md)。

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>如何將大量文件插入 Cosmos DB？

您可以利用下列任一方式將大量文件插入 Azure Cosmos DB：

* 大量執行程式工具，如[使用大量執行程式工具 .NET 程式庫](bulk-executor-dot-net.md)和[使用大量執行程式工具 Java 程式庫](bulk-executor-java.md)中所述
* 資料移轉工具，如 [Azure Cosmos DB 的資料庫移轉工具](import-data.md)中所述。
* 預存程序，如 [Azure Cosmos DB 的伺服器端 JavaScript 程式設計](stored-procedures-triggers-udfs.md)中所述。

### <a name="does-the-sql-api-support-resource-link-caching"></a>SQL API 支援資源連結快取嗎？

是，因為 Azure Cosmos DB 是一項 RESTful 服務，資源連結是固定不變且可快取的。 SQL API 用戶端可以指定一個"如果無匹配"標頭,用於讀取任何類似資源的文檔或容器,然後在伺服器版本更改後更新其本地副本。

### <a name="is-a-local-instance-of-sql-api-available"></a>SQL API 的本機執行個體可供使用嗎？

是。 [Azure Cosmos DB 模擬器](local-emulator.md)提供 Cosmos DB 服務的高逼真度模擬。 它支援與 Azure Cosmos DB 完全相同的功能，包括支援建立和查詢 JSON 文件、佈建和擴充集合，以及執行預存程序和觸發程序。 您可以使用 Azure Cosmos DB 模擬器來開發及測試應用程式，並且只需對 Azure Cosmos DB 的連接端點進行單一組態變更，就能將它們部署至全球規模的 Azure。

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>在入口網站中從資料總管檢視文件中的長浮點值時，這些值為何會四捨五入。

這是 JavaScript 的限制。 JavaScript 使用 IEEE 754 中指定的雙精度浮點格式編號,它只能安全地將數位保留在 -(2<sup>53</sup> - 1) 和 2<sup>53</sup>-1(即 9007199254740991)之間。

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>物件階層中允許哪些權限？

您可以在容器層級和其子系 (例如文件、附件) 使用 ResourceTokens 來建立權限。 這意味著目前不允許在資料庫或帳戶層級嘗試建立權限。

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>什麼是適用於 MongoDB 的 Azure Cosmos DB API？

Azure Cosmos DB 的 MongoDB API 是一個有線協定相容性層,它允許應用程式通過使用蒙戈DB的現有、社區支援的 SDK 和驅動程式輕鬆、透明地與本機 Azure Cosmos 資料庫引擎通信。 開發人員現在可以使用現有的 MongoDB 工具鏈和技能來建構利用 Azure Cosmos DB 的應用程式。 開發人員受益於 Azure Cosmos DB 的獨特功能,其中包括具有多主機複製、自動索引、備份維護、財務支援的服務級別協定 (SL) 等的全域分發。

### <a name="how-do-i-connect-to-my-database"></a>我要如何連線到我的資料庫？

若要使用適用於 MongoDB 的 Azure Cosmos DB API 來連線至 Cosmos 資料庫，最快的方式是直接前往 [Azure 入口網站](https://portal.azure.com)。 移至您的帳戶，然後在左側瀏覽功能表中按一下 [快速入門]****。 快速入門是獲取代碼段連接到資料庫的最佳方式。

Azure Cosmos DB 會強制執行嚴格的安全性需求和標準。 Azure Cosmos DB 帳戶需要透過 TLS 進行身份驗證和安全通訊,因此請務必使用 TLSv1.2。

如需詳細資訊，請參閱[使用適用於 MongoDB 的 Azure Cosmos DB API 連線到 Cosmos 資料庫](connect-mongodb-account.md)。

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>使用適用於 MongoDB 的 Azure Cosmos DB API 時，是否有其他必須處理的錯誤碼？

除了常見的 MongoDB 錯誤碼，適用於 MongoDB 的 Azure Cosmos DB API 有專用錯誤碼：

| 錯誤               | 程式碼  | 描述  | 解決方法  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | 使用的請求單位總數超過容器的預配請求單位速率,並且已被限制。 | 請考慮從 Azure 入口網站調整指派給容器或容器集的輸送量，或重試一次。 |
| ExceededMemoryLimit | 16501 | 做為多租用戶服務，作業已超出用戶端的記憶體配額。 | 透過更嚴格的查詢準則來縮小作業的範圍，或經由 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)連絡支援人員。 <br><br>範例:db.getCollection("<em>&nbsp;&nbsp;&nbsp;&nbsp;使用者")。聚合(*<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[$match:[名稱:"安迪"*, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[$sort: [年齡: -1]<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Simba 驅動程式是否支援與適用於 MongoDB 的 Azure Cosmos DB API 搭配使用？

是的,您可以將辛巴的蒙戈 ODBC 驅動程式與 Azure Cosmos DB 的蒙戈DB API 一起使用

## <a name="table-api"></a><a id="table"></a>資料表 API

### <a name="how-can-i-use-the-table-api-offering"></a>如何使用資料表 API 供應項目？

[Azure 入口網站][azure-portal]中已提供 Azure Cosmos DB 資料表 API。 首先，您必須註冊 Azure 訂用帳戶。 註冊之後，您可以將 Azure Cosmos DB 資料表 API 帳戶加入 Azure 訂用帳戶，接著再將資料表加入帳戶。

您可以在 [Azure Cosmos DB 資料表 API 簡介](table-introduction.md)中找到支援的語言和相關快速入門。

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>我需要新的 SDK，才能使用資料表 API 嗎？

不必，現有的儲存體 SDK 應該還是有效。 不過，建議您隨時取得最新的 SDK 以獲得最佳支援，以及在諸多情況下獲得更優異的效能。 請參閱 [Azure Cosmos DB 資料表 API 簡介](table-introduction.md)中的可用語言清單。

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>資料表 API 與 Azure 資料表儲存體的行為有哪些不同？

來自 Azure 資料表儲存體而想要使用 Azure Cosmos DB 資料表 API 來建立資料表的使用者，應該注意以下的部分行為差異：

* Azure Cosmos DB 資料表 API 使用保留容量模型來確保效能獲得保證，但這也表示，使用者一建立資料表，就必須支付其容量費用，即使該容量並未使用也是如此。 而在使用 Azure 資料表儲存體時，使用者只需要就使用的容量付費。 此一行為有助於解釋，為何資料表 API 可於第 99 個百分位數的水準提供 10 毫秒的讀取和 15 毫秒的寫入 SLA，而 Azure 資料表儲存體則是提供 10 秒的 SLA。 但也因為這樣，資料表 API 的資料表 (甚至是沒有任何要求的空白資料表) 必須付出金錢，才能確保有容量可用來處理其所收到的任何要求，並以 Azure Cosmos DB 所提供的 SLA 來進行。
* 表 API 傳回的查詢結果不像在 Azure 表儲存中那樣按分區鍵/ 行鍵順序排序。
* 資料列索引鍵最多只能是 255 個位元組
* 批次最多只可有 2 MB
* 目前不支援 CORS
* Azure 表儲存中的表名稱不區分大小寫,但它們位於 Azure Cosmos DB 表 API 中
* 部分 Azure Cosmos DB 用於編碼資訊 (例如二進位欄位) 的內部格式，目前不如預期地有效率。 因此這會對資料大小造成非預期的限制。 例如，目前無法使用資料表實體的完整 1 Meg 來儲存二進位資料，因為編碼方式增加了資料的大小。
* 當前不支援的實體屬性名稱"ID"
* TableQuery TakeCount 不限於 1000

就 REST API 來說，Azure Cosmos DB 資料表 API 並不支援某些端點/查詢選項：

| REST 方法 | REST 端點/查詢選項 | 文件 URL | 說明 |
| ------------| ------------- | ---------- | ----------- |
| GET、PUT | /?restype=service@comp=properties| [設定資料表服務屬性](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties)和[取得資料表服務屬性](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | 此端點可用來設定 CORS 規則、儲存體分析組態以及記錄設定。 CORS 目前不受支援，而且分析和記錄在 Azure Cosmos DB 和 Azure 儲存體資料表中的處理方式不同 |
| OPTIONS | /\<表資源名稱> | [事前 CORS 資料表要求](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | 這是 CORS 的一部分，但目前不受 Azure Cosmos DB 支援。 |
| GET | /?restype=service@comp=stats | [取得資料表服務統計資料](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | 提供主要與次要資料庫之間的資料複寫速度有多快的資訊。 因為複寫是寫入的一部分，因此這不是 Cosmos DB 中的必須作業。 |
| GET、PUT | /mytable?comp=acl | [取得資料表 ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) 和[設定資料表 ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | 這會取得並設定用來管理共用存取簽章 (SAS) 的預存存取原則。 雖然 SAS 有受到支援，但其設定和管理方式並不相同。 |

此外，Azure Cosmos DB 資料表 API 只支援 JSON 格式，而不支援 ATOM。

雖然 Azure Cosmos DB 支援共用存取簽章 (SAS)，但它也有某些不支援的原則，具體地說，它不支援與管理作業有關的原則，例如用以建立新資料表的權限。

.NET SDK 尤其如此，裡面會有一些 Azure Cosmos DB 目前不支援的類別和方法。

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

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>如何提供關於 SDK 和 Bug 的意見反應？

您可以使用以下任何方式來分享意見反應：

* [使用者語音](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN 論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [堆疊溢出](https://stackoverflow.com/questions/tagged/azure-cosmosdb)。 Stack Overflow 最適合用於詢問程式設計問題。 確保您的問題[契合主題](https://stackoverflow.com/help/on-topic)且[盡可能提供愈多的詳細資料，讓問題更加清楚並可得到答覆](https://stackoverflow.com/help/how-to-ask)。

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>我需要哪一個連接字串，才能連接資料表 API？

連接字串為：

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
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

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>假設我一開始就有 n** GB 的資料，而我的資料將隨著時間過去成長為 1 TB，我該如何針對此服務完成儲存體大小的擴充？

Azure Cosmos DB 的設計乃藉由使用水平調整提供無限制的儲存體。 這項服務能監視並有效地增加您的儲存體。

### <a name="how-do-i-monitor-the-table-api-offering"></a>如何監視資料表 API 供應項目？

您可以使用資料表 API 的 [計量]**** 窗格，來監視要求和儲存體使用量。

### <a name="how-do-i-calculate-the-throughput-i-require"></a>如何計算我需要的輸送量？

您可以使用的容量估計工具來計算作業所需的 TableThroughput。 如需詳細資訊，請參閱[估算要求單位和資料儲存體 (英文)](https://www.documentdb.com/capacityplanner)。 通常，您可以將實體顯示為 JSON，並為您的作業提供編號。

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>我可以在本機搭配使用資料表 API SDK 與模擬器嗎？

目前沒有。

### <a name="can-my-existing-application-work-with-the-table-api"></a>我現有的應用程式可以與資料表 API 搭配運作嗎？

是，可支援相同的 API。

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>如果我不想要使用「資料表 API」功能，是否需要將現有的「Azure 資料表」儲存體應用程式移轉至 SDK？

否，您可以建立和使用現有的「Azure 資料表」儲存體資產，而不會有任何形式的服務中斷。 不過，如果您不使用資料表 API ，就無法從自動索引、額外的一致性選項或全域散發中獲益。

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>如何在「資料表 API」中新增跨多個 Azure 區域的資料複寫？

可以使用 Azure Cosmos DB 門戶的[全域複製設置](tutorial-global-distribution-sql-api.md#portal)添加適合應用程式的區域。 若要開發全域散發的應用程式，您還應該使用設為本地區域的 PreferredLocation 資訊來加入您的應用程式，以提供低讀取延遲。

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>如何在「資料表 API」中變更帳戶的主要寫入區域？

您可以使用 Azure Cosmos DB 的全球複寫入口網站窗格來加入一個區域，然後容錯移轉至所需的區域。 有關說明,請參閱[使用多區域 Azure Cosmos DB 帳號進行開發](high-availability.md)。

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>當我散發資料時，如何設定慣用的讀取區域以取得低延遲？

若要協助讀取本機位置，請使用 app.config 檔案中的 PreferredLocation 金鑰 。 對於現有的應用程式，如果您已設定 LocationMode，資料表 API 會擲回錯誤。 請移除該程式碼，因為資料表 API 會從 app.config 檔案取用這項資訊。 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>如何看待資料表 API 中的一致性層級？

Azure Cosmos DB 可在一致性、高可用性及延遲之間提供完全合乎邏輯的取捨。 Azure Cosmos DB 資料表 API 提供開發人員五種一致性層級，所以您可以在資料表層級選擇合適的一致性模型，以及在查詢資料時進行個別要求。 當用戶端連線時，它可以指定一致性層級。 您可以透過 CreateCloudTableClient 的 consistencyLevel 引數來變更層級。

資料表 API 會利用「讀取您自己的寫入」來提供低延遲的讀取，並採用限定過期一致性的預設值。 如需詳細資訊，請參閱[一致性層級](consistency-levels.md)。

根據預設，Azure 資料表儲存體提供區域內的強式一致性，以及次要位置中最終的一致性。

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>「Azure Cosmos DB 資料表 API」是否提供比「Azure 資料表」更多的一致性層級？

是，如需如何從 Azure Cosmos DB 分散式本質獲益的相關資訊，請參閱[一致性層級](consistency-levels.md)。 由於我們提供一致性層級保證，因此您可以放心使用。

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>啟用全域散發時，複寫資料需要多久的時間？

Azure Cosmos DB 會在當地區域持久認可資料，並在幾毫秒內立即將資料推送到其他區域。 這項複寫操作只取決於資料中心的來回時間 (RTT)。 若要深入了解 Azure Cosmos DB 的全域散發能力，請參閱 [Azure Cosmos DB︰Azure 上分散在世界各地的資料庫服務](distribute-data-globally.md)。

### <a name="can-the-read-request-consistency-level-be-changed"></a>可以變更讀取要求一致性層級嗎？

有了 Azure Cosmos DB，您可以設定容器層級的一致性層級 (在資料表中)。 透過 .NET SDK，您可以在 app.config 檔案中提供 TableConsistencyLevel 索引鍵的值，藉以變更層級。 可能的值包括：強式、限定過期、工作階段、一致前置詞或最終。 如需詳細資訊，請參閱 [Azure Cosmos DB 中的 Tunable 資料一致性層級](consistency-levels.md)。 主要概念在於，您無法將要求一致性層級設定為高於資料表設定的值。 例如，您無法將資料表的一致性層級設定為「最終」，而將要求一致性層級設定為「強式」。

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>當區域發生服務中斷時，「資料表 API」如何處理容錯移轉？

資料表 API 會利用 Azure Cosmos DB 的全域散發平台。 若要確保您的應用程式可以容許資料中心停機，請在 Azure Cosmos DB 入口網站中針對該帳戶至少啟用一個以上的區域，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。 您可以使用入口網站來設定區域的優先順序，[使用多區域 Azure Cosmos DB 帳戶進行開發](high-availability.md)。

您可以針對帳戶加入任意數目的區域，並藉由提供容錯移轉優先順序來控制容錯移轉的目標位置。 若要使用的資料庫，您還需要在目標位置提供應用程式。 如果您這樣做，您的客戶就不會經歷停機時間。 [最新的 .NET 用戶端 SDK](table-sdk-dotnet.md) 會自動連接，但其他 SDK 則不會。 也就是說，它可以偵測到區域已關閉，並自動容錯移轉至新的區域。

### <a name="is-the-table-api-enabled-for-backups"></a>是否已啟用「資料表 API」的備份功能？

是，資料表 API 會利用 Azure Cosmos DB 的平台來進行備份。 備份會自動執行。 如需詳細資訊，請參閱[使用 Azure Cosmos DB 進行線上備份及還原](online-backup-and-restore.md)。

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>資料表 API 預設會為實體的所有屬性編製索引嗎？

是，根據預設，它會為實體的所有屬性編製索引。 有關詳細資訊,請參閱[Azure 宇宙資料庫:索引原則](index-policy.md)。

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>這表示我不需要建立多個索引來滿足查詢嗎？

是，Azure Cosmos DB 資料表 API 會為所有屬性提供自動編製索引，而不需任何結構描述定義。 這項資訊能讓開發人員專注於應用程式，而不需擔心索引的建立和管理。 有關詳細資訊,請參閱[Azure 宇宙資料庫:索引原則](index-policy.md)。

### <a name="can-i-change-the-indexing-policy"></a>我可以變更索引編制原則嗎？

是，您可以提供索引定義來變更索引編制原則。 您需要適當地進行編碼和逸出這些設定。

針對非 .NET SDK，索引編製原則只能在入口網站的**資料總管**中進行設定，請瀏覽至您想要變更的特定資料表，然後移至 [規模與設定]**** -> [索引編製原則]，進行所需的變更，然後 [儲存]****。

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
* 您需要更多來自後端容器或容器集的輸送量。 例如，您發現使用的輸送量比佈建的輸送量還多，而您正要開始進行節流。 有關詳細資訊,請參閱設定[Azure Cosmos 容器的輸送量](set-throughput.md)。

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>我可以相應增加或減少資料表 API 資料表的輸送量嗎？

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

Azure Cosmos DB 是 SLA 型系統，它提供無限制的調整和延遲、輸送量、可用性、一致性保證。 若要獲得保證的進階效能，您必須確定資料大小和索引是可管理且可調整的。 每個資料分割索引鍵的實體或項目數目限制為 10 GB，這是為了確保我們能夠提供絕佳的查閱和查詢效能。 為了確保您的應用程式能夠針對 Azure 儲存體進行更佳的調整，建議您「避免」** 建立常用資料分割 (將所有資訊儲存在一個資料分割，然後進行查詢)。

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>這樣說來，資料表 API 仍然需要 PartitionKey 和 RowKey 嗎？

是。 因為「資料表 API」的介面區與「Azure 資料表」儲存體 SDK 的介面區類似，所以分割區索引鍵能提供有效率的資料散發方式。 資料列索引鍵在該資料分割內是唯一的。 資料列索引鍵必須存在且不能為 Null，如同在標準 SDK 中。 RowKey 的長度是 255 個位元組，而 PartitionKey 為 1 KB。

### <a name="what-are-the-error-messages-for-the-table-api"></a>資料表 API 的錯誤訊息將有哪些？

Azure 資料表儲存體和 Azure Cosmos DB 資料表 API 使用相同的 SDK，因此大部分的錯誤會相同。

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>當我嘗試在資料表 API 中逐一建立許多資料表時，為什麼要進行節流？

Azure Cosmos DB 是 SLA 型系統，可提供延遲、輸送量、可用性及一致性的保證。 由於它是佈建的系統，因此會保留資源以保證這些需求。 系統會偵測到快速建立的資料表，並加以節流。 我們建議您查看資料表的建立速率，並降低為低於每分鐘 5 個。 請記住，資料表 API 是佈建的系統。 只要您一佈建，就必須開始支付費用。

## <a name="gremlin-api"></a>Gremlin API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>若是使用 C#/.NET 開發，應該使用 Microsoft.Azure.Graphs 套件或 Gremlin.NET？

Azure Cosmos DB Gremlin API 利用開放原始碼驅動程式作為服務的主要連接器。 因此，建議使用受 [ Apache Tinkerpop 支援的驅動程式](https://tinkerpop.apache.org/)。

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>在圖形資料庫中執行查詢時，每秒的 RU 如何計費？

包括頂點及邊線的所有圖形物件，都會以 JSON 文件形式於後端顯示。 由於一個 Gremlin 查詢一次可以修正一或多個圖形物件，因此與其相關的成本會與這些由查詢處理的物件及邊線有直接關係。 Azure Cosmos DB 使用相同流程處理其他所有的 API。 有關詳細資訊,請參閱[Azure Cosmos DB 中的請求單位](request-units.md)。

RU 計費是根據周遊的運作資料庫計算，並不是根據結果集計算。 比方說，若查詢的目標是要獲得單一頂點作為結果，但需要同時周遊其他多個物件，則會依計算單一結果頂點所需的所有圖形物件來計算成本。

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>圖形資料庫在 Azure Cosmos DB Gremlin API 中的最大比例是多少?

Azure Cosmos DB 使用[水平資料分割](partition-data.md)，自動處理儲存體和輸送量需要增加的問題。 工作負載的最大輸送量和存儲容量由與給定容器關聯的分區數決定。 但是,Gremlin API 容器具有一組特定的準則,以確保大規模獲得適當的性能體驗。 如需有關分割的詳細資訊及最佳做法，請參閱[在 Azure Cosmos DB 中進行資料分割](partition-data.md)一文。

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>如何防止透過 Gremlin 驅動程式發動的資料隱碼攻擊？

大多數本機 Apache Tinkerpop Gremlin 驅動程式允許提供用於查詢執行的參數位典。 這裡提供使用 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) 和 [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js) 兩種方法的執行範例。

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>為什麼我收到「格林林查詢編譯錯誤:找不到任何方法」錯誤?

Azure Cosmos DB Gremlin API 會實作一個在 Gremlin 介面區定義的功能子集。 如需支援的步驟和詳細資訊，請參閱 [Gremlin 支援](gremlin-support.md)一文。

最好的解決方法，是以支援的功能重新寫入必要的 Gremlin 步驟，因為 Azure Cosmos DB 支援所有必要的 Gremlin 步驟。

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>為什麼收到「WebSocket 異常:當狀態代碼」101「錯誤時伺服器返回狀態代碼」200"?

系統會在使用了錯誤的端點時擲回此錯誤。 產生此錯誤的端點模式如下：

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

這是供您圖形資料庫使用的文件端點。  正確的端點是 Gremlin Endpoint，其格式如下：

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>為什麼我收到「請求率太大」錯誤?

此錯誤表示，已配置的每秒要求單位不足以提供查詢之所用。 通常您會在執行會獲得所有頂點的查詢時看到此錯誤：

```
// Query example:
g.V()
```

此查詢會嘗試從圖形中擷取所有頂點。 因此，此查詢的成本至少等於 RU 的頂點數目。 您應該調整每秒 RU 的設定，才能處理此查詢。

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>為什麼我的 Gremlin 驅動程式連線最後還是失敗？

Gremlin 連線是透過 WebSocket 連線而產生的。 雖然 WebSocket 連線並沒有特定的存留時間，Azure Cosmos DB Gremlin API 仍然會在 30 分鐘的非使用狀態之後終止閒置的連線。

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>為什麼我不能在本機 Gremlin 驅動程式中使用流暢的 API 調用?

Azure Cosmos DB Gremlin API 尚未支援 Fluent API 呼叫。 Fluent API 呼叫需要稱為位元組程式碼支援的內部格式化功能，目前 Azure Cosmos DB Gremlin API 不支援此功能。 由於相同的原因，最新的 Gremlin-JavaScript 驅動程式目前也不受支援。

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>如何評估 Gremlin 查詢的效率？

**executionProfile()** 預覽步驟可提供查詢執行計畫的分析。 您需要將此步驟新增至任何 Gremlin 查詢的結尾，如以下範例所示：

**查詢範例**

```
g.V('mary').out('knows').executionProfile()
```

**範例輸出**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

上述配置檔的輸出顯示獲取頂點物件、邊緣物件以及工作數據集的大小所花費的時間。 這與 Azure Cosmos DB 查詢的標準成本量值相關。

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
