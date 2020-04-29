---
title: Azure Cosmos DB： SQL Node.js API、SDK & 資源
description: 了解所有 SQL Node.js API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB Node.js SDK 每個版本之間所做的變更。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982883"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API：版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 變更摘要](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行程式-JAVA](sql-api-sdk-bulk-executor-java.md)

|資源  |連結  |
|---------|---------|
|下載 SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 文件  |  [JavaScript SDK 參考文件](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK 安裝指示  |  [安裝指示](https://github.com/Azure/azure-cosmos-js#installation)
|參與 SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 範例 | [Node.js 程式碼範例](sql-api-nodejs-samples.md)
| 入門教學課程 | [開始使用 JavaScript SDK](sql-api-nodejs-get-started.md)
| Web 應用程式教學課程 | [使用 Azure Cosmos DB 來建置 Node.js Web 應用程式](sql-api-nodejs-application.md)
| 目前支援的平台 | [Node.js v12.](https://nodejs.org/en/blog/release/v12.7.0/) x-SDK 3.x. x 版<br/>[Node.js v10.](https://nodejs.org/en/blog/release/v10.6.0/) x-SDK 3.x. x 版<br/>[Node.js v8.](https://nodejs.org/en/blog/release/v8.16.0/) x-SDK 3.x. x 版<br/>[Node.js 3.x](https://nodejs.org/en/blog/release/v6.10.3/) . x SDK 2.x 版<br/>[Node.js v 4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)-SDK 1.x 版<br/> [Node.js v 0.12](https://nodejs.org/en/blog/release/v0.12.0/)-SDK 1.x 版<br/> [Node.js v 0.10](https://nodejs.org/en/blog/release/v0.10.0/)-SDK 1.x 版

## <a name="release-notes"></a>版本資訊

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* 將預設 ResponseContinuationTokenLimitInKB 設定為1kb。 根據預設，我們會將此值上限為 1 kb，以避免長標頭（node.js 具有全域標頭大小限制）。 使用者可以將此欄位設定為允許較長的標頭，以協助後端優化查詢執行。
* 移除 disableSSLVerification。 此選項具有中所述的新替代專案[#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* 允許 initialHeaders 明確設定分割區索引鍵標頭
* 使用 package. json # 檔案以防止發行多餘的檔案
* 修正舊版節點的路由對應排序錯誤 + v8
* 修正使用者提供部分重試選項時的 bug

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* 防止 Webpack 解析以要求呼叫的模組

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* 修正長時間未處理的錯誤，其中 ru 一律會回報為0以進行匯總查詢

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 版本！ 🎉許多新功能、bug 修正和一些重大變更。 此版本的主要目標：

* 實行主要的新功能
  * 相異查詢
  * 限制/位移查詢
  * 使用者可取消要求
* 更新至最新的 Cosmos REST API 版本，其中所有容器都具有無限制的規模
* 讓您更輕鬆地從瀏覽器使用 Cosmos
* 進一步配合新的 Azure JS SDK 指導方針

#### <a name="migration-guide-for-breaking-changes"></a>突破性變更的遷移指南
##### <a name="improved-client-constructor-options"></a>改良的用戶端函式選項

已簡化的函式選項：

* masterKey 已重新命名為金鑰，並已移至最上層
* 先前在選項下的屬性。 auth 已移至最上層

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>簡化的 QueryIterator API
在 v2 中，有許多不同的方式可逐一查看或抓取查詢的結果。 我們嘗試簡化 v3 API，並移除類似或重複的 Api：

* 移除 iterator。 next （）和 iterator. current （）。 使用 fetchNext （）來取得結果的頁面。
* 移除 iterator. forEach （）。 請改用非同步反覆運算器。
* iterator. executeNext （）已重新命名為 iterator. fetchNext （）
* iterator. toArray （）已重新命名為 iterator. fetchAll （）
* 頁面現在是適當的回應物件，而不是純 JS 物件
* const container = client. database （dbId）。 container （containerId）

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>固定的容器現在已分割
Cosmos 服務現在支援所有容器上的資料分割索引鍵，包括先前建立為固定容器的索引鍵。 V3 SDK 會更新為可執行此變更的最新 API 版本，但不會中斷。 如果您未提供作業的分割區索引鍵，我們會預設為與所有現有容器和檔搭配使用的系統金鑰。

##### <a name="upsert-removed-for-stored-procedures"></a>已移除預存程式的 Upsert
非分割的集合允許先前的 upsert，但使用 API 版本更新時，所有集合都已分割，因此我們已將其完全移除。

##### <a name="item-reads-will-not-throw-on-404"></a>專案讀取不會在404上擲回
const container = client. database （dbId）。 container （containerId）

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>預設多區域寫入
根據預設，如果您的 Cosmos 設定支援，SDK 現在會寫入至多個區域。 這是先前選擇的行為。

##### <a name="proper-error-objects"></a>適當的錯誤物件
失敗的要求現在會擲回適當的錯誤或錯誤子類別。 先前它們會擲回一般 JS 物件。

#### <a name="new-features"></a>新功能
##### <a name="user-cancelable-requests"></a>使用者可取消的要求
「移至內部提取」可讓我們使用瀏覽器 AbortController API 來支援使用者可取消的作業。 在有多個要求可能正在進行的作業（例如跨分割區查詢）中，將會取消該作業的所有要求。 現代化的瀏覽器使用者已經 AbortController。 Node.js 使用者將需要使用 polyfill 程式庫

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>將輸送量設定為 db/容器建立作業的一部分
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
標頭權杖產生已分割為新的程式庫@azure/cosmos-sign。 呼叫 Cosmos REST API 的任何人都可以直接使用此來簽署標頭，使用我們在@azure/cosmos內部呼叫的相同程式碼。

##### <a name="uuid-for-generated-ids"></a>產生之識別碼的 UUID
v2 有自訂程式碼可產生專案識別碼。 我們已切換為知名且維護的社區程式庫 uuid。

##### <a name="connection-strings"></a>連接字串
現在可以傳遞從 Azure 入口網站複製的連接字串：

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>改良的瀏覽器體驗
雖然您可以在瀏覽器中使用 v2 SDK，但它並不是理想的體驗。 您需要 polyfill 數個 node.js 內建程式庫，並使用 Webpack 或包裹之類的搭配程式。 V3 SDK 讓瀏覽器使用者的體驗變得更好。

* 以 fetch 取代要求內部（#245）
* 移除緩衝區的使用方式（#330）
* 移除節點內建使用，以改用通用套件/Api （#328）
* 切換至節點-中止-控制器（#294）

#### <a name="bug-fixes"></a>錯誤修正
* 修正供應專案讀取和返回供應專案測試（#224）
* 修正 EnableEndpointDiscovery （#207）
* 修正分頁結果上遺漏的 ru （#360）
* 展開 SQL 查詢參數類型（#346）
* 將 ttl 新增至 ItemDefinition （#341）
* 修正 CP 查詢計量（#311）
* 將 activityId 新增至 FeedResponse （#293）
* 將字串中的 _ts 類型切換成數位（#252）（#295）
* 修正要求費用匯總（#289）
* 允許空白字串分割區索引鍵（#277）
* 將字串加入衝突查詢類型（#237）
* 將 uniqueKeyPolicy 新增至容器（#234）

#### <a name="engineering-systems"></a>工程系統
不一定是最明顯的變更，但可協助我們的小組以更快的速度提供更好的程式碼。

* 針對生產組建使用匯總套件（#104）
* 更新至 Typescript 3.5 （#327）
* 轉換成 TS 專案參考。 解壓縮測試檔案夾（#270）
* 啟用 noUnusedLocals 和 noUnusedParameters （#275）
* CI 組建的 Azure Pipelines YAML （#298）

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* 無程式碼變更。 修正2.1.4 套件中包含一些額外檔案的問題。

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* 修正重試原則內的區域容錯移轉
* 修正 Changefeed program.cs hasMoreResults 屬性
* 開發相依性更新
* 新增 PolicheckExclusions .txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* 將字串中的 _ts 類型切換為數字
* 修正預設的索引測試
* 將 uniqueKeyPolicy 至 v2
* 示範和示範的偵錯工具修正

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* 從 v3 分支將供應專案修正
* 修正 executeNext （）類型簽章中的 bug
* 錯誤修正

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* 組建重建。 允許在組建階段提取 SDK 版本。

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>新功能
* 已新增 Changefeed program.cs 支援（#196）
* 已新增用於編制索引的 MultiPolygon 資料類型（#191）
* 將 "key" 屬性新增至函式做為 masterKey 的別名（#202）

#### <a name="fixes"></a>修正
* 修正 next （）在 iterator 上傳回不正確值的 bug

#### <a name="engineering-improvements"></a>工程改進
* 新增 typescript 耗用量的整合測試（#199）
* 啟用直接從 GitHub 安裝（#194）

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* 已新增適用於節點代理程式類型的介面。 Typescript 使用者不再需要安裝 @types/node 作為相依性
* 現在會正確接受慣用位置
* 提供開發人員文件的改進
* 各種錯字修正

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* 修正 2.0.3 中所導入的類型定義問題

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* 移除 `big-integer` 相依性
* 切換為適用於 AsyncIterable 類型的參考指示詞。 Typescript 使用者不再需要自訂他們的 "lib" 設定。
* 錯字修正

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* 修正讀我檔案連結

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* 修正重試介面實作

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* JavaScript SDK 2.0.0 版的公開推出
* 已新增對多重區域寫入的支援。

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* JavaScript SDK 2.0.0 版的 RC1 (用於公開預覽)。
* 新的物件模型，具有最上層 CosmosClient，且方法分成相關的資料庫、容器和項目類別。 
* 支援 [Promise](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises)。 
* SDK 轉換成 TypeScript。

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* 修正的 npm 文件。

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* 已新增對連線問題預設重試次數的支援。
* 已新增對讀取集合變更摘要的支援。
* 已修正間歇造成「無法讀取工作階段」的工作階段一致性錯誤。
* 已新增對查詢度量資訊的支援。
* 已修正 http 代理程式的最大連接數目。

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* 更新參考 Azure Cosmos DB 而非 Azure DocumentDB 的文件。
* 在 ConnectionPolicy 加入對 proxyUrl 設定的支援。

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* 區分大小寫的檔案系統的次要修正。

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* 新增工作階段一致性的支援。
* 此 SDK 版本需要最新版本的[Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)。

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* 分割已經過證明的跨分割區查詢。
* 新增對資源連結開頭和尾端斜線 (和對應的測試) 的支援。

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    修正的 npm 文件。

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* 已修正 executeStoredProcedure 中的錯誤，其中涉及的文件有特殊 Unicode 字元 (LS、PS)。
* 已修正在分割區索引鍵中使用 Unicode 字元處理文件的錯誤。
* 已修正使用名稱媒體建立集合的支援。 GitHub 問題 #114。
* 已修正權限授權權杖的支援。 GitHub 問題 #178。

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* 新增對新[一致性層級](consistency-levels.md) ConsistentPrefix 的支援。
* 新增 UriFactory 的支援。
* 已修正 Unicode 支援錯誤。 GitHub 問題 #171。

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。
* 新增控制跨分割區查詢平行處理程度的選項。
* 已新增在針對 Azure Cosmos DB 模擬器執行時停用 TLS 驗證的選項。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。
* 已修正單一資料分割集合的接續權杖錯誤。 GitHub 問題 #107。
* 已修正將 0 做為單一參數處理的 executeStoredProcedure 錯誤。 GitHub 問題 #155。

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* 修正使用者-代理程式標頭以包含 SDK 版本。
* 次要程式碼清除。

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* 使用 SDK 以模擬器為目標時，停用 TLS 驗證（hostname = localhost）。
* 在預存程序執行期間，加入支援指令碼記錄功能。

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* 新增對跨資料分割平行查詢的支援。
* 新增對已分割集合的 TOP/ORDER BY 查詢支援。

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* 新加入已節流處理要求的重試原則支援。 （節流的要求會收到太大的要求速率，錯誤碼429。）根據預設，當遇到錯誤碼429時，Azure Cosmos DB 會針對每個要求重試九次，並接受回應標頭中的 retryAfter 時間。 如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。 Azure Cosmos DB 現在會針對每個要進行節流處理的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。 您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。
* Cosmos DB 現在會傳回 x-ms-throttle-retry-count 和 x-ms-throttle-retry-wait-time-ms 做為每個要求的回應標頭，其代表節流重試計數和要求歷經多次重試的累積時間。
* 新增 RetryOptions 類別，以及公開 ConnectionPolicy 類別上的 RetryOptions 屬性，使其能用來覆寫某些預設的重試選項。

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* 新增對多重區域資料庫帳戶的支援。

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* 新加入文件的存留時間 (TTL) 功能支援。

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* 實作[已分割的集合](partition-data.md)和[使用者定義的效能等級](performance-levels.md)。

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* 已修正 RangePartitionResolver.resolveForRead 錯誤，其因錯誤的結果 CONCAT 而無法傳回連結。

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* 修正 hashPartitionResolver resolveForRead()：所提供的資料分割索引鍵都未擲回例外狀況時，而不會傳回所有已註冊連結的清單。

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* 修正問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100)- HTTPS 專用代理程式：避免基於 Azure Cosmos DB 用途而修改全域代理程式。 針對所有 lib 的要求使用專用的代理程式。

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* 修正問題 [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - 正確處理媒體識別碼中的連字號。

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* 修正問題 [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter 接聽程式洩漏警告。

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* 修正問題 [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - 針對區分大小寫的系統，將資料夾 Hash 重新命名為 hash。

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* 藉由新增雜湊和範圍分割解析程式來實作分區化支援。

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* 實作 Upsert。 documentClient 上新的 upsertXXX 方法。

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* 已略過以配合其他 SDK 的版本號碼。

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* 將 Q Pomise 包裝函式分割至新的儲存機制。
* 更新至 npm 登錄的封裝檔案。

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* 實作以識別碼為基礎的路由。
* 修正問題 [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - 目前屬性與 current() 方法衝突。

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* 新增對地理空間索引的支援。
* 驗證所有資源的識別碼屬性。 資源的識別碼不能包含？、/、#、 &#47;&#47;、字元，或以空格結尾。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* 實作 V2 索引原則。

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* 問題 [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - 核心與 Promise SDK 中實作的 eslint 和 grunt 組態。

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* 問題 [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Promise 包裝函式不包括標頭並發生錯誤。

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* 已實作透過新增 readConflicts、readConflictAsync 及 queryConflicts 來查詢衝突的功能。
* 已更新 API 文件。
* 問題 [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync 錯誤。

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK。

## <a name="release--retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

服務將會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

> [!WARNING]
> 適用于 SQL **API 的所有1.x 版 Node**用戶端 SDK 將于**2020 年8月30日**淘汰。 這只會影響用戶端節點 SDK，而且不會影響伺服器端腳本（預存程式、觸發程式和 Udf）。
> 
>
<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 7 月 26 日 |--- |
| [3.0.4](#3.0.4) |2019年7月22日 |--- |
| [3.0.3](#3.0.3) |2019 年 7 月 17 日 |--- |
| [3.0.2](#3.0.2) |2019 年 7 月 9 日 |--- |
| [3.0.0](#3.0.0) |2019年6月28日 |--- |
| [2.1.5](#2.1.5) |2019年3月20日 |--- |
| [2.1.4](#2.1.4) |2019年3月15日 |--- |
| [2.1.3](#2.1.3) |2019年3月8日 |--- |
| [2.1.2](#2.1.2) |2019 年 1 月 28 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 5 日 |--- |
| [2.1.0](#2.1.0) |2018 年 12 月 4 日 |--- |
| [2.0.5](#2.0.5) |2018 年 11 月 7 日 |--- |
| [2.0.4 版](#2.0.4) |2018年10月30日 |--- |
| [2.0.3](#2.0.3) |2018年10月30日 |--- |
| [2.0.2](#2.0.2) |2018 年 10 月 10日 |--- |
| [2.0.1](#2.0.1) |2018 年 9 月 25 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 24 日 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |2018 年 8 月 2 日 |--- |
| [1.14.4](#1.14.4) |2018 年 5 月 3 日 |2020年8月30日 |
| [1.14.3](#1.14.3) |2018 年 5 月 3 日 |2020年8月30日 |
| [1.14.2](#1.14.2) |2017 年 12 月 21 日 |2020年8月30日 |
| [1.14.1](#1.14.1) |2017 年 11 月 10 日 |2020年8月30日 |
| [1.14.0](#1.14.0) |2017 年 11 月 9 日 |2020年8月30日 |
| [1.13.0](#1.13.0) |2017 年 10 月 11 日 |2020年8月30日 |
| [1.12.2](#1.12.2) |2017 年 8 月 10 日 |2020年8月30日 |
| [1.12.1](#1.12.1) |2017 年 8 月 10 日 |2020年8月30日 |
| [1.12.0](#1.12.0) |2017 年 5 月 10 日 |2020年8月30日 |
| [1.11.0](#1.11.0) |2017 年 3 月 16 日 |2020年8月30日 |
| [1.10.2](#1.10.2) |2017 年 1 月 27 日 |2020年8月30日 |
| [1.10.1 版](#1.10.1) |2016 年 12 月 22 日 |2020年8月30日 |
| [1.10.0](#1.10.0) |2016 年 10 月 3 日 |2020年8月30日 |
| [1.9.0](#1.9.0) |2016 年 7 月 7 日 |2020年8月30日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020年8月30日 |
| [1.7.0](#1.7.0) |2016 年 4 月 26 日 |2020年8月30日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020年8月30日 |
| [1.5.6](#1.5.6) |2016 年 3 月 8 日 |2020年8月30日 |
| [1.5.5](#1.5.5) |2016 年 2 月 2 日 |2020年8月30日 |
| [1.5.4](#1.5.4) |2016 年 2 月 1 日 |2020年8月30日 |
| [1.5.2](#1.5.2) |2016 年 1 月 26 日 |2020年8月30日 |
| [1.5.2](#1.5.2) |2016 年 1 月 22 日 |2020年8月30日 |
| [1.5.1](#1.5.1) |2016 年 1 月 4 日 |2020年8月30日 |
| [1.5。0](#1.5.0) |2015 年 12 月 31 日 |2020年8月30日 |
| [1.4.0](#1.4.0) |2015 年 10 月 6 日 |2020年8月30日 |
| [1.3.0](#1.3.0) |2015 年 10 月 6 日 |2020年8月30日 |
| [1.2.2](#1.2.2) |2015 年 9 月 10 日 |2020年8月30日 |
| [1.2.1](#1.2.1) |2015 年 8 月 15 日 |2020年8月30日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020年8月30日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020年8月30日 |
| [1.0.3](#1.0.3) |2015 年 6 月 4 日 |2020年8月30日 |
| [1.0.2](#1.0.2) |2015 年 5 月 23 日 |2020年8月30日 |
| [1.0.1](#1.0.1) |2015 年 5 月 15 日 |2020年8月30日 |
| [1.0.0](#1.0.0) |2015 年 4 月 8 日 |2020年8月30日 |

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

