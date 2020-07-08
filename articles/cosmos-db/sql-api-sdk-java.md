---
title: Azure Cosmos DB：SQL Java API、SDK 和資源
description: 了解所有 SQL Java API 和 SDK 相關資訊，包括發行日期、停用日期及 Azure Cosmos DB SQL Java SDK 每個版本之間所做的變更。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 06/03/2020
ms.author: anfeldma
ms.openlocfilehash: 520f64cbe768f2fcbd4603d1fb038bc349cd01db
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391973"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>適用於 SQL API 的 Azure Cosmos DB Java SDK：版本資訊和資源
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 變更摘要 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [非同步 Java SDK v2](sql-api-sdk-async-java.md)
> * [同步 Java SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST 資源提供者](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [大量執行程式-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [大量執行工具 - Java](sql-api-sdk-bulk-executor-java.md)

這是適用於 SQL API 的原始 Azure Cosmos DB 同步 Java SDK v2，可支援同步作業。

> [!IMPORTANT]  
> 這「不是」適用於 Azure Cosmos DB 的最新 Java SDK！ 請考慮針對您的專案使用 [Azure Cosmos DB Java SDK v4](sql-api-sdk-java-v4.md)。 若要升級，請遵循[移轉到 Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) 指南及 [Reactor 與 RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md) 指南中的指示。 
>

| |  |
|---|---|
|**SDK 下載**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API 文件**|[Java API 參考文件](/java/api/com.microsoft.azure.documentdb)|
|**參與 SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**開始使用**|[開始使用 Java SDK](sql-api-java-get-started.md)|
|**Web 應用程式教學課程**|[使用 Azure Cosmos DB 進行 Web 應用程式開發](sql-api-java-application.md)|
|**最低支援執行階段**|[Java 開發套件 (JDK) 7+](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>版本資訊

### <a name="251"></a><a name="2.5.1"></a>2.5.1
* 修正 documentCollection 查詢的主要分割區快取問題。

### <a name="250"></a><a name="2.5.0"></a>2.5.0
* 已新增449重試自訂設定的支援。

### <a name="247"></a><a name="2.4.7"></a>2.4.7
* 修正連接集區逾時問題。
* 修正內部重試時的驗證權杖重新整理。

### <a name="246"></a><a name="2.4.6"></a>2.4.6
* 更新 databaseAccount 上正確的用戶端複本原則標籤，並且可從快取中讀取 databaseAccount 設定。

### <a name="245"></a><a name="2.4.5"></a>2.4.5
* 如果使用者提供 pkRangeId，則避免在出現無效分割區索引鍵範圍錯誤時重試。

### <a name="244"></a><a name="2.4.4"></a>2.4.4
* 最佳化分割區索引鍵範圍快取重新整理。
* 修正 SDK 不接受來自伺服器的分割區分割提示，導致用戶端路由快取重新整理的情況。

### <a name="242"></a><a name="2.4.2"></a>2.4.2
* 最佳化集合快取重新整理。

### <a name="241"></a><a name="2.4.1"></a>2.4.1
* 新增從要求診斷字串擷取內部例外狀況訊息的支援。

### <a name="240"></a><a name="2.4.0"></a>2.4.0
* 推出 PartitionKeyDefinition 的 api 版本。

### <a name="230"></a><a name="2.3.0"></a>2.3.0
* 新增適用於直接模式的個別逾時支援。

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* 從服務取用 Null 錯誤訊息，並產生文件用戶端例外狀況。

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* 通訊端連線改善，新增 SoKeepAlive 預設值為 true。

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* 新增要求診斷字串支援。

### <a name="213"></a><a name="2.1.3"></a>2.1.3
* 修正適用於 Hash V2 的 PartitionKey 的錯誤 (bug)。

### <a name="212"></a><a name="2.1.2"></a>2.1.2
* 新增複合式索引的支援。
* 修正全域端點管理員強制執行重新整理的錯誤 (bug)。
* 修正直接模式中具備先決條件的 upsert 的錯誤 (bug)。

### <a name="211"></a><a name="2.1.1"></a>2.1.1
* 修正閘道位址快取中的錯誤 (bug)。

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* 針對直接模式新增多重區域寫入支援。
* 新增處理從 Proxy 擲回為 ServiceUnavailable 例外狀況之 IOExceptions 的支援。
* 修正端點探索重試原則中的錯誤 (bug)。
* 修正錯誤 (bug) 以確保不會在 BaseDatabaseAccountConfigurationProvider 中擲回 Null 指標例外狀況。
* 修正錯誤 (bug) 以確保 QueryIterator 不會傳回 Null。
* 修正錯誤 (bug) 以確保允許大型 PartitionKey

### <a name="200"></a><a name="2.0.0"></a>2.0.0
* 針對閘道模式新增多重區域寫入支援。

### <a name="1164"></a><a name="1.16.4"></a>1.16.4
* 修正查詢之讀取分割區索引鍵範圍中的錯誤 (bug)。

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* 修正在 DirectHttps 模式中設定接續權杖標頭大小的錯誤 (bug)。

### <a name="1162"></a><a name="1.16.2"></a>1.16.2
* 已新增串流容錯移轉支援。
* 已新增自訂中繼資料的支援。
* 已改善工作階段處理邏輯。
* 已修正分割區索引鍵範圍快取中的 Bug。
* 已修正直接模式中的 NPE Bug。

### <a name="1161"></a><a name="1.16.1"></a>1.16.1
* 新增唯一索引的支援。
* 新增摘要選項中的接續權杖大小限制支援。
* 修正 Json 序列化的錯誤 (bug) (時間戳記)。
* 修正 Json 序列化的錯誤 (bug) (列舉)。
* 將 com.fasterxml.jackson.core:jackson-databind 的相依性升級至 2.9.5。

### <a name="1160"></a><a name="1.16.0"></a>1.16.0
* 改善直接模式的連線共用。
* 增強跨資料分割查詢的非 orderby 預先擷取改良。
* 改善 UUID 產生作業。
* 改善工作階段一致性邏輯。
* 新增對 Multipolygon 的支援。
* 新增集合的分割區索引鍵範圍統計支援。
* 修正多重區域中的錯誤 (bug)。

### <a name="1150"></a><a name="1.15.0"></a>1.15.0
* 以改善的 Json 序列化效能。
* 此 SDK 版本需要使用最新版本的 [Azure Cosmos DB 模擬器](https://aka.ms/cosmosdb-emulator)。

### <a name="1140"></a><a name="1.14.0"></a>1.14.0
* Microsoft 贊助者程式庫的內部變更。

### <a name="1130"></a><a name="1.13.0"></a>1.13.0
* 修正讀取單一分割區金鑰範圍的問題。
* 修正會影響到名稱簡短的資料庫的 ResourceID 剖析問題。
* 修正分割區金鑰編碼產生的問題。

### <a name="1120"></a><a name="1.12.0"></a>1.12.0
* 重大錯誤修正，要求在分割區分割期間處理。
* 已利用 Strong 和 BoundedStaleness 一致性層級修正問題。

### <a name="1110"></a><a name="1.11.0"></a>1.11.0
* 已新增對名為 ConsistentPrefix 的新一致性層級的支援。
* 已修正工作階段模式中讀取集合的錯誤。

### <a name="1100"></a><a name="1.10.0"></a>1.10.0
* 已啟用對低至 2,500 RU/秒且以 100 RU/秒遞增量進行調整之資料分割集合的支援。
* 已修正原生組件中的 Bug，這會在某些查詢中導致 NullRef 例外狀況。

### <a name="196"></a><a name="1.9.6"></a>1.9.6
* 修正查詢引擎組態中可能對閘道模式中的查詢造成例外狀況的錯誤。
* 修正工作階段容器中可能在集合建立後立即對要求造成「找不到擁有者資源」例外狀況的幾個錯誤。

### <a name="195"></a><a name="1.9.5"></a>1.9.5
* 新增彙總查詢的支援 (COUNT、MIN、MAX、SUM 和 AVG)。 請參閱[彙總支援](sql-query-aggregates.md)。
* 新增變更摘要的支援。
* 新增透過 RequestOptions.setPopulateQuotaInfo 收集配額資訊的支援。
* 新增透過 RequestOptions.setScriptLoggingEnabled 進行預存程序指令碼記錄的支援。
* 修正發生節流失敗時，DirectHttps 模式的查詢可能會停止回應的錯誤 (bug)。
* 修正工作階段一致性模式中的錯誤。
* 修正當要求率過高時，可能在 HttpContext 中造成 NullReferenceException 的錯誤。
* 改善 DirectHttps 模式的效能。

### <a name="194"></a><a name="1.9.4"></a>1.9.4
* 在 ConnectionPolicy.setProxy() API 加入簡單的用戶端執行個體 Proxy 支援。
* 加入的 DocumentClient.close() API 可正確關閉 DocumentClient 執行個體。
* 從原生組件 (而不是從閘道) 衍生的查詢計劃利用直接連線模式改善查詢效能。
* 設定 FAIL_ON_UNKNOWN_PROPERTIES = false，讓使用者不需要在其 POJO 定義 JsonIgnoreProperties。
* 重新建構記錄使用 SLF4J。
* 修正一致性讀取器中的其他幾個 Bug。

### <a name="193"></a><a name="1.9.3"></a>1.9.3
* 修正連線管理中的 Bug，避免連線在直接連線模式中流失。
* 修正排名最前的查詢中可能會擲回 NullReference 例外狀況的 Bug。
* 透過減少內部快取的網路呼叫數來增進效能。
* 在 DocumentClientException 中的 ActivityID 和要求 URI 中加入狀態碼以協助疑難排解。

### <a name="192"></a><a name="1.9.2"></a>1.9.2
* 修正連線管理中的問題以提供穩定性。

### <a name="191"></a><a name="1.9.1"></a>1.9.1
* 新增對 BoundedStaleness 一致性層級的支援。
* 新增對已分割集合之 CRUD 作業的直接連線支援。
* 修正以 SQL 查詢資料庫時發生的錯誤。
* 修正工作階段權杖設定不正確之工作階段快取中的錯誤。

### <a name="190"></a><a name="1.9.0"></a>1.9.0
* 新增對跨資料分割平行查詢的支援。
* 新增對已分割集合的 TOP/ORDER BY 查詢支援。
* 新增對強式一致性的支援。
* 新增對使用直接連線時之名稱型要求的支援。
* 修正以使得 ActivityId 在所有要求重試之間保持一致。
* 修正與以相同名稱重新建立集合時之工作階段快取相關的錯誤。
* 新增為異地隔離空間查詢指定集合索引編製原則時的 Polygon 和 LineString 資料類型。
* 修正適用於 Java 1.8 之 Java Doc 的錯誤。

### <a name="181"></a><a name="1.8.1"></a>1.8.1
* 修正 PartitionKeyDefinitionMap 中將單一資料分割集合加入快取，而非提出額外擷取資料分割索引鍵要求的錯誤。
* 修正在提供不正確的資料分割索引鍵時不重試的錯誤。

### <a name="180"></a><a name="1.8.0"></a>1.8.0
* 新增對多重區域資料庫帳戶的支援。
* 新增在已節流處理的要求上自動重試的支援，方法是提供選項來自訂重試次數上限和等待時間上限。  請參閱 RetryOptions 和 ConnectionPolicy.getRetryOptions()。
* 已淘汰以 IPartitionResolver 為基礎的自訂分割程式碼。 請針對更高的儲存體和輸送量使用分割集合。

### <a name="171"></a><a name="1.7.1"></a>1.7.1
* 已新增速率限制的重試原則支援。  

### <a name="170"></a><a name="1.7.0"></a>1.7.0
* 新加入文件的存留時間 (TTL) 支援。

### <a name="160"></a><a name="1.6.0"></a>1.6.0
* 實作[已分割的集合](partition-data.md)和[使用者定義的效能等級](performance-levels.md)。

### <a name="151"></a><a name="1.5.1"></a>1.5.1
* 修正 HashPartitionResolver 中的錯誤以產生與其他 SDK 一致的 little-endian 雜湊值。

### <a name="150"></a><a name="1.5.0"></a>1.5.0
* 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="140"></a><a name="1.4.0"></a>1.4.0
* 實作 Upsert。 已新增新的 upsertXXX 方法以支援 Upsert 功能。
* 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="130"></a><a name="1.3.0"></a>1.3.0
* 已略過版本以配合其他 SDK 的版本號碼

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* 支援地理空間索引
* 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、\, 字元，或以空格作為結尾。
* 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* 實作 V2 索引原則

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>發行和停用日期
Microsoft 至少會在停用 SDK 的 **12 個月** 之前提供通知，以供順利轉換至較新/支援的版本。

新的功能與最佳化項目只會新增至目前的 SDK，因此建議您一律盡早升級至最新的 SDK 版本。

服務將會拒絕使用已停用 SDK 的任何 Cosmos DB 要求。

> [!WARNING]
> SQL SDK for Java 的所有版本 **1.x** 將於 **2020 年 5 月 30 日**淘汰。
> 
>

> [!WARNING]
> 所有 **1.0.0** 版之前的 SQL SDK 版本都將於「2016 年 2 月 29 日」淘汰。
> 
> 

<br/>

| 版本 | 發行日期 | 停用日期 |
| --- | --- | --- |
| [2.5.1](#2.5.1) |2020年6月03日 |--- |
| [2.5.0](#2.5.0) |2020 年 5 月 12 日 |--- |
| [2.4.7](#2.4.7) |2020 年 2 月 20 日 |--- |
| [2.4.6](#2.4.6) |2020 年 1 月 24 日 |--- |
| [2.4.5](#2.4.5) |2019 年 11 月 10 日 |--- |
| [2.4.4](#2.4.4) |2019 年 10 月 24 日 |--- |
| [2.4.2](#2.4.2) |2019 年 9 月 26 日 |--- |
| [2.4.1](#2.4.1) |2019 年 7 月 18 日 |--- |
| [2.4.0](#2.4.0) |2019 年 5 月 4 日 |--- |
| [2.3.0](#2.3.0) |2019 年 4 月 24 日 |--- |
| [2.2.3](#2.2.3) |2019 年 4 月 16 日 |--- |
| [2.2.2](#2.2.2) |2019 年 4 月 5 日 |--- |
| [2.2.0](#2.2.0) |2019 年 3 月 27 日 |--- |
| [2.1.3](#2.1.3) |2019 年 3 月 13 日 |--- |
| [2.1.2](#2.1.2) |2019 年 3 月 9 日 |--- |
| [2.1.1](#2.1.1) |2018 年 12 月 13 日 |--- |
| [2.1.0](#2.1.0) |2018 年 11 月 20 日 |--- |
| [2.0.0](#2.0.0) |2018 年 9 月 21 日 |--- |
| [1.16.4](#1.16.4) |2018 年 9 月 10 日 |2020 年 5 月 30 日 |
| [1.16.3](#1.16.3) |2018 年 9 月 9 日 |2020 年 5 月 30 日 |
| [1.16.2](#1.16.2) |2018 年 6 月 29 日 |2020 年 5 月 30 日 |
| [1.16.1](#1.16.1) |2018 年 5 月 16 日 |2020 年 5 月 30 日 |
| [1.16.0](#1.16.0) |2018 年 3 月 15 日 |2020 年 5 月 30 日 |
| [1.15.0](#1.15.0) |2017 年 11 月 14 日 |2020 年 5 月 30 日 |
| [1.14.0](#1.14.0) |2017 年 10 月 28 日 |2020 年 5 月 30 日 |
| [1.13.0](#1.13.0) |2017 年 8 月 25 日 |2020 年 5 月 30 日 |
| [1.12.0](#1.12.0) |2017 年 7 月 11 日 |2020 年 5 月 30 日 |
| [1.11.0](#1.11.0) |2017 年 5 月 10 日 |2020 年 5 月 30 日 |
| [1.10.0](#1.10.0) |2017 年 3 月 11 日 |2020 年 5 月 30 日 |
| [1.9.6](#1.9.6) |2017 年 2 月 21 日 |2020 年 5 月 30 日 |
| [1.9.5](#1.9.5) |2017 年 1 月 31 日 |2020 年 5 月 30 日 |
| [1.9.4](#1.9.4) |2016 年 11 月24 日 |2020 年 5 月 30 日 |
| [1.9.3](#1.9.3) |2016 年 10 月 30 日 |2020 年 5 月 30 日 |
| [1.9.2](#1.9.2) |2016 年 10 月 28 日 |2020 年 5 月 30 日 |
| [1.9.1](#1.9.1) |2016 年 10 月 26 日 |2020 年 5 月 30 日 |
| [1.9.0](#1.9.0) |2016 年 10 月 3 日 |2020 年 5 月 30 日 |
| [1.8.1](#1.8.1) |2016 年 6 月 30 日 |2020 年 5 月 30 日 |
| [1.8.0](#1.8.0) |2016 年 6 月 14 日 |2020 年 5 月 30 日 |
| [1.7.1](#1.7.1) |2016 年 4 月 30 日 |2020 年 5 月 30 日 |
| [1.7.0](#1.7.0) |2016 年 4 月 27 日 |2020 年 5 月 30 日 |
| [1.6.0](#1.6.0) |2016 年 3 月 29 日 |2020 年 5 月 30 日 |
| [1.5.1](#1.5.1) |2015 年 12 月 31 日 |2020 年 5 月 30 日 |
| [1.5.0](#1.5.0) |2015 年 12 月 4 日 |2020 年 5 月 30 日 |
| [1.4.0](#1.4.0) |2015 年 10 月 5 日 |2020 年 5 月 30 日 |
| [1.3.0](#1.3.0) |2015 年 10 月 5 日 |2020 年 5 月 30 日 |
| [1.2.0](#1.2.0) |2015 年 8 月 5 日 |2020 年 5 月 30 日 |
| [1.1.0](#1.1.0) |2015 年 7 月 9 日 |2020 年 5 月 30 日 |
| 1.0.1 |2015 年 5 月 12 日 |2020 年 5 月 30 日 |
| [1.0.0](#1.0.0) |2015 年 4 月 7 日 |2020 年 5 月 30 日 |
| 0.9.5-prelease |2015 年 3 月 9 日 |2016 年 2 月 29 日 |
| 0.9.4-prelease |2015 年 2 月 17 日 |2016 年 2 月 29 日 |
| 0.9.3-prelease |2015 年 1 月 13 日 |2016 年 2 月 29 日 |
| 0.9.2-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.1-prelease |2014 年 12 月 19 日 |2016 年 2 月 29 日 |
| 0.9.0-prelease |2014 年 12 月 10日 |2016 年 2 月 29 日 |

## <a name="faq"></a>常見問題集
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>另請參閱
若要深入了解 Cosmos DB，請參閱 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 服務頁面。

