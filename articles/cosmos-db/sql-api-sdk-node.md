---
title: Azure 宇宙資料庫:SQL Node.js API、SDK &资源
description: 了解所有 SQL Node.js API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB Node.js SDK 每個版本之間所做的變更。
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 181f43e060e3b4b41babeee375365a97e1d224aa
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411277"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK for SQL API：版本資訊與資源
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET 更改來源](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [非同步 Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST 資源提供者](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [批次執行器 -.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [批次執行器 -Java](sql-api-sdk-bulk-executor-java.md)

|資源  |連結  |
|---------|---------|
|下載 SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API 文件  |  [JavaScript SDK 參考文件](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|SDK 安裝指示  |  [安裝說明](https://github.com/Azure/azure-cosmos-js#installation)
|參與 SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| 範例 | [Node.js 程式碼範例](sql-api-nodejs-samples.md)
| 入門教學課程 | [開始使用 JavaScript SDK](sql-api-nodejs-get-started.md)
| Web 應用程式教學課程 | [使用 Azure Cosmos DB 來建置 Node.js Web 應用程式](sql-api-nodejs-application.md)
| 目前支援的平台 | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK 版本 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK 版本 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK 版本 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK 版本 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK 版本 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK 版本 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK 版本 1.x.x

## <a name="release-notes"></a>版本資訊

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* 將默認回應延續權杖限制InKB設置為1kb。 默認情況下,我們將此限制為 1kb 以避免長標頭(Node.js 具有全域標頭大小限制)。 用戶可以將此欄位設置為允許較長的標頭,這有助於後端優化查詢執行。
* 刪除禁用 SSL 驗證。 這個選項具有[#388](https://github.com/Azure/azure-cosmos-js/pull/388)中介紹的新備選方案

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* 允許初始標題顯示式設定分區金鑰標頭
* 使用套件.json_檔案防止發佈無關檔
* 修復舊版本的節點_v8上的路由映射排序錯誤
* 當使用者提供部分重試選項時修復 Bug

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* 防止 Webpack 解析需要呼叫的模組

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* 修復了一個長期未解決的 Bug,其中聚合查詢的 R 統始終報告為 0

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 版本! 🎉 許多新功能、錯誤修復和一些重大更改。 此版本的主要目標:

* 實現主要新功能
  * 二元查詢
  * 限制/位移查詢
  * 使用者可取消的要求
* 更新到最新的 Cosmos REST API 版本,其中所有容器都有無限比例
* 輕鬆地從瀏覽器使用宇宙
* 更好地與新的 Azure JS SDK 指南保持一致

#### <a name="migration-guide-for-breaking-changes"></a>中斷變更的移轉指南
##### <a name="improved-client-constructor-options"></a>改進的客戶端建構函數選項

建構函數選項已簡化:

* masterKey 已重新命名金鑰並移至頂層
* 以前在選項.auth 下的屬性已移到頂層

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

##### <a name="simplified-queryiterator-api"></a>簡化查詢反覆發程式器 API
在 v2 中,從查詢中反覆運算或檢索結果的方法有很多種。 我們試圖簡化 v3 API 並刪除類似或重複的 API:

* 刪除反覆運算器.next() 和反覆運算器.current()。 使用 fetchNext() 獲取結果頁。
* 刪除反覆運算器.foreach()。 改用異步反覆運算器。
* 反覆發器.executeNext() 重新命名為反覆運算器.fetchNext()
* 反覆發代器.toArray() 重新命名為反覆運算器.fetchAll()
* 頁面現在是正確的回應物件,而不是普通的 JS 物件
* const 容器 = 客戶端.資料庫(dbId.容器)容器 Id

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

##### <a name="fixed-containers-are-now-partitioned"></a>固定容器已分割區
Cosmos 服務現在支援所有容器的分區鍵,包括以前創建為固定容器的容器。 v3 SDK 更新到實現此更改的最新 API 版本,但它並未中斷。 如果不為操作提供分區密鑰,我們將默認為適用於所有現有容器和文檔的系統金鑰。

##### <a name="upsert-removed-for-stored-procedures"></a>為儲存過程移除了 Upsert
以前允許對非分區集合進行 upsert,但隨著 API 版本更新,所有集合都進行了分區,因此我們完全刪除了它。

##### <a name="item-reads-will-not-throw-on-404"></a>專案讀取不會扔在 404 上
const 容器 = 客戶端.資料庫(dbId.容器)容器 Id

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
如果 Cosmos 配置支援 SDK,則默認情況下,SDK 將寫入多個區域。 這是以前選擇加入的行為。

##### <a name="proper-error-objects"></a>正確的錯誤物件
失敗的請求現在引發正確的錯誤或錯誤子類。 以前,他們拋出普通的JS物件。

#### <a name="new-features"></a>新功能
##### <a name="user-cancelable-requests"></a>使用者可取消的要求
內部獲取的移動允許我們使用瀏覽器 AbortController API 來支援使用者可取消的操作。 對於可能正在進行多個請求的操作(如跨分區查詢),將取消該操作的所有請求。 現代瀏覽器用戶已經具有 AbortController。 Node.js 使用者將需要使用多填充庫

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>將輸送量設定為 db/容器建立操作的一部分
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
標頭權杖產生被分割庫@azure/cosmos-sign。 直接呼叫Cosmos REST API的任何人都可以使用它使用@azure/cosmos我們在內部呼叫的代碼對標頭進行簽名。

##### <a name="uuid-for-generated-ids"></a>產生的識別碼的 UUID
v2 具有生成項目 ID 的自訂代碼。 我們已經轉到了眾所周知和維護的社區圖書館。

##### <a name="connection-strings"></a>連接字串
現在可以傳遞從 Azure 門戶複製的連接字串:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>改進瀏覽器體驗
雖然在瀏覽器中使用 v2 SDK 是可能的,但這不是理想的體驗。 您需要對多個 node.js 內建庫進行多邊填充,並使用 Webpack 或 Parcel 等捆綁包。 v3 SDK 使開箱即用的體驗對瀏覽器使用者更好。

* 將要求內部取代為提取(#245)
* 刪除緩衝區的使用(#330)
* 刪除節點內建用法,以選擇通用包/API (#328)
* 切換到節點中止控制器 (#294)

#### <a name="bug-fixes"></a>錯誤修正
* 修復產品/服務讀取並帶回產品/服務測試(#224)
* 修復啟用終結點發現(#207)
* 修復分頁結果上缺少的 R,#360)
* 展開 SQL 查詢參數型態 (#346)
* 將 ttl 新增到項目定義 (#341)
* 修復 CP 查詢指標 (#311)
* 將活動 Id 加入來源回應 (#293)
* 將_ts類型從字串切換到數位(#252)(#295)
* 修復要求費用聚合(#289)
* 允許空白字串分割區鍵 (#277)
* 將字串新增到衝突查詢型態 (#237)
* 新增為容器加入唯一的關鍵政策 (#234)

#### <a name="engineering-systems"></a>專案系統
並不總是最明顯的變化,但它們有助於我們的團隊更快地運送更好的代碼。

* 對生產產生(#104)使用匯總
* 更新到 Typescript 3.5 (#327)
* 轉換為 TS 專案引用。 擷取測試資料夾 (#270)
* 開啟無未使用的本地變數和未使用參數(#275)
* 用於 CI 產生的 Azure 管道 YAML(#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* 沒有代碼更改。 修復了 2.1.4 包中包含一些額外文件的問題。

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* 修復重新試策略中的區域故障移轉
* 修復變更來源有更多結果屬性
* 開發相依項更新
* 新增 Policheck 排除.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* 將_ts類型從字串切換到數字
* 修復預設索引測試
* 回埠唯一的關鍵策略到 v2
* 簡報及展示除錯程式

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* 從 v3 分支提供修復
* 修復執行Next() 類型簽署的 Bug
* Typo 修復

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* 構建重組。 允許在生成時拉取 SDK 版本。

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>新功能
* 新增了變更來源支援(#196)
* 新增了用於索引的多多邊形資料類型(#191)
* 將「鍵」屬性新增到建構函數作為主金鑰 (#202) 的別名

#### <a name="fixes"></a>修正
* 修復在反覆發代器上傳回錯誤值的位置

#### <a name="engineering-improvements"></a>專案改進
* 新增類型文稿消耗的整合測試(#199)
* 直接從 GitHub (#194) 啟用安裝

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
* 此 SDK 版本需要使用從 https://aka.ms/cosmosdb-emulator 下載之最新版本的 Azure Cosmos DB 模擬器。

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* 分割已經過證明的跨分割區查詢。
* 新增對資源連結開頭和尾端斜線 (和對應的測試) 的支援。

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*   修正的 npm 文件。

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
* 添加了在針對 Azure Cosmos DB 模擬器運行時禁用 TLS 驗證的選項。
* 已將分割區集合的最小輸送量從 10,100 RU/s 降低為 2500 RU/s。
* 已修正單一資料分割集合的接續權杖錯誤。 GitHub 問題 #107。
* 已修正將 0 做為單一參數處理的 executeStoredProcedure 錯誤。 GitHub 問題 #155。

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* 修正使用者-代理程式標頭以包含 SDK 版本。
* 次要程式碼清除。

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* 使用 SDK 定位模擬器(主機名+本地主機)時禁用 TLS 驗證。
* 在預存程序執行期間，加入支援指令碼記錄功能。

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* 新增對跨資料分割平行查詢的支援。
* 新增對已分割集合的 TOP/ORDER BY 查詢支援。

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* 新加入已節流處理要求的重試原則支援。 (已限制的請求收到請求速率過大的異常,錯誤代碼 429。預設情況下,當遇到錯誤代碼 429 時,Azure Cosmos DB 會為每個請求重試 9 次,從而在響應標頭中遵守重試時間。 如果您想要忽略伺服器在多次重試之間傳回的 retryAfter 時間，現在可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中設定固定的重試間隔時間。 Azure Cosmos DB 現在會針對每個要進行節流處理的要求等候最多 30 秒 (不論重試計數為何)，並傳回包含錯誤碼 429 的回應。 您也可以在 ConnectionPolicy 物件上的 RetryOptions 屬性中覆寫該時間。
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
* 修正問題 [#100](https://github.com/Azure/azure-documentdb-node/issues/100)- HTTPS 專用代理程式：避免基於 Azure Cosmos DB 用途而修改全域代理程式。 針對所有 lib 要求使用專用的代理程式。

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
* 驗證所有資源的識別碼屬性。 資源的帳號不能包含""/"、*、 &#47;&#47;、字元或以空格結尾。
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
> SQL API 的節點用戶端 SDK 的所有版本**1.x**將於**2020 年 8 月 30**日停用。 這僅影響用戶端節點 SDK,不會影響伺服器端腳本(儲存過程、觸發器和 UDF)。
> 
>
<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [3.1.0](#3.1.0) |2019 年 7 月 26 日 |--- |
| [3.0.4](#3.0.4) |2019年7月22日 |--- |
| [3.0.3](#3.0.3) |2019 年 7 月 17 日 |--- |
| [3.0.2](#3.0.2) |2019 年 7 月 9 日 |--- |
| [3.0.0](#3.0.0) |六月 28, 2019 |--- |
| [2.1.5](#2.1.5) |2019年3月20日 |--- |
| [2.1.4](#2.1.4) |2019年3月15日 |--- |
| [2.1.3](#2.1.3) |2019年3月8日 |--- |
| [2.1.2](#2.1.2) |2019 年 1 月 28 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 5 日 |--- |
| [2.1.0](#2.1.0) |2018 年 12 月 4 日 |--- |
| [2.0.5](#2.0.5) |2018 年 11 月 7 日 |--- |
| [2.0.4](#2.0.4) |2018年10月30日 |--- |
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
| [1.10.1](#1.10.1) |2016 年 12 月 22 日 |2020年8月30日 |
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
| [1.5.0](#1.5.0) |2015 年 12 月 31 日 |2020年8月30日 |
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

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

