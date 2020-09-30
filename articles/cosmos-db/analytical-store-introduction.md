---
title: 什麼是 Azure Cosmos DB 分析存放區 (預覽)？
description: 深入了解 Azure Cosmos DB 交易 (以資料列為基礎) 和分析 (以資料行為基礎) 存放區。 分析存放區的優點、大規模工作負載的效能影響，以及將資料從交易存放區自動同步至分析存放區
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: rosouz
ms.openlocfilehash: d27eb4dc6c4e4bd8f0a744ad925d91aee0faa8d0
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567140"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>什麼是 Azure Cosmos DB 分析存放區 (預覽)？

> [!IMPORTANT]
> Azure Cosmos DB 分析存放區目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

Azure Cosmos DB 分析存放區是完全隔離的資料行存放區，可針對您 Azure Cosmos DB 中的運算元據啟用大規模分析，而不會影響您的交易式工作負載。  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>大規模分析操作資料的挑戰

Azure Cosmos DB 容器中的多模型操作資料會在內部儲存於以索引資料列為基礎的「交易存放區」中。 資料列存放區格式旨在加速交易讀取及寫入 (以毫秒為單位的回應時間) 和操作查詢。 如果您的資料整合長大型，複雜的分析查詢在以這種格式儲存的資料上可能會耗用大量的布建輸送量。 布建輸送量的高耗用量，會影響即時應用程式和服務所使用的交易式工作負載效能。

在過去，若要分析大量資料，會從 Azure Cosmos DB 的交易存放區中擷取操作資料，並加以儲存在不同的資料層中。 例如，資料會以適當的格式儲存在資料倉儲或 Data Lake 中。 此資料稍後會用於大規模分析，並使用計算引擎 (例如 Apache Spark 叢集) 進行分析。 將分析儲存體和計算層與操作資料分開，會導致額外的延遲，因為 ETL (擷取、轉換、載入) 管線的執行頻率較低，從而將交易工作負載的潛在影響降至最低。

相較於只處理新內嵌的操作資料，ETL 管線在處理操作資料的更新時，也會變得很複雜。 

## <a name="column-oriented-analytical-store"></a>資料行導向的分析存放區

Azure Cosmos DB 分析存放區可解決傳統 ETL 管線所發生的複雜性和延遲挑戰。 Azure Cosmos DB 分析存放區可以將您的操作資料自動同步處理到個別的資料行存放區。 資料行存放區格式適合以優化方式執行大規模分析查詢，進而改善這類查詢的延遲。

您現在可以使用 Azure Synapse Link，直接從 Synapse Analytics 連結至 Azure Cosmos DB 分析存放區，來建置無 ETL HTAP 解決方案。 其可讓您針對操作資料執行接近即時的大規模分析。

## <a name="features-of-analytical-store"></a>分析存放區的功能 

當您在 Azure Cosmos DB 容器上啟用分析存放區時，會根據您容器中的操作資料，在內部建立新的資料行存放區。 此資料行存放區會與該容器的資料列導向交易存放區分開保存。 您的操作資料插入、更新和刪除作業會自動同步處理到分析存放區。 您不需要變更摘要或 ETL 來同步處理資料。

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>適用於操作資料分析工作負載的資料行存放區

分析工作負載通常包含所選取欄位的匯總和連續掃描。 分析存放區會以資料行為主的順序來儲存資料，讓每個欄位的一組值序列化在一起。 此格式可減少掃描或計算特定欄位之統計資料所需的 IOPS。 其可大幅改善掃描大型資料集的查詢回應時間。 

例如，如果您的操作表格採用下列格式：

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="範例操作表格" border="false":::

資料列存放區會在磁片上以序列化格式 (每一列) 保存上述資料。 此格式可加速交易讀取、寫入和操作查詢，例如「傳回 Product1 的相關資訊」。 不過，當資料集擴增，且如果您想要對資料執行複雜的分析查詢，則可能會所費不貲。 例如，如果您想要取得「在不同的業務單位和月份，名為「設備」類別中的產品銷售趨勢」，則必須執行複雜查詢。 在此資料集上進行大量掃描，則佈建的輸送量可能會所費不貲，且也可能會影響驅動您即時應用程式和服務的交易工作負載效能。

分析存放區是一種資料行存放區，較適合這類查詢，因為其會將類似的資料欄位序列化在一起，並減少磁碟 IOPS。

下圖顯示 Azure Cosmos DB 中的交易資料列存放區與分析資料行存放區：

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="範例操作表格" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>分析工作負載的低耦合效能

由於分析存放區與交易存放區分開，因此不會因分析查詢而影響您交易工作負載的效能。  分析存放區不需要配置個別的要求單位 (RU)。

### <a name="auto-sync"></a>自動同步

自動同步處理是指 Azure Cosmos DB 的完全受控功能，其中作業資料的插入、更新、刪除作業會以近乎即時的方式自動從交易式存放區同步處理至分析存放區。 自動同步處理延遲通常是在2分鐘內。 如果有大量容器的共用輸送量資料庫，則個別容器的自動同步延遲可能會較高，且最多需要5分鐘的時間。 我們想要深入瞭解此延遲如何符合您的案例。 若要這樣做，請與 [Azure Cosmos DB 團隊](mailto:cosmosdbsynapselink@microsoft.com)聯繫。

自動同步功能和分析存放區提供下列主要優點：

#### <a name="scalability--elasticity"></a>可擴縮性和彈性

Azure Cosmos DB 交易存放區會使用水平資料分割，彈性地縮放儲存體和輸送量，而不需要停機。 交易存放區中的水平資料分割會提供自動同步的可擴縮性和彈性，以確保資料近乎即時地同步到分析存放區。 無論交易流量輸送量為何 (不論為 1000 個作業/秒或 100 萬個作業/秒)，都會進行資料同步，且不會影響交易存放區中佈建的輸送量。 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>自動處理架構更新

Azure Cosmos DB 交易存放區無從驗證結構描述，可讓您逐一查看交易應用程式，而不需處理結構描述或索引管理。 相較之下，Azure Cosmos DB 分析存放區會進行結構描述，以針對分析查詢效能進行最佳化。 利用自動同步處理功能，Azure Cosmos DB 管理交易式存放區中最新更新的架構推斷。  還會管理現成分析存放區中的結構描述表示，包括處理嵌套的巢狀資料類型。

隨著您的架構演進，以及隨著時間新增新的屬性，分析存放區會自動在交易式存放區中的所有歷程記錄架構之間呈現聯集架構。

##### <a name="schema-constraints"></a>架構條件約束

當您啟用分析存放區以自動推斷並正確地表示架構時，下列條件約束適用于 Azure Cosmos DB 中的運算元據：

* 在架構中的任何嵌套層級，最多可以有200個屬性，而最大的嵌套深度為5。
  
  * 最上層具有201屬性的專案不符合此條件約束，因此不會在分析存放區中表示。
  * 在架構中具有超過五個嵌套層級的專案也不符合此條件約束，因此不會在分析存放區中表示。 例如，下列專案不符合需求：

     `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* 比較案例區分大小寫時，屬性名稱必須是唯一的。 例如，下列專案不符合此條件約束，因此不會在分析存放區中表示：

  `{"Name": "fred"} {"name": "john"}` -在以不區分大小寫的方式進行比較時，「名稱」和「名稱」相同。

##### <a name="schema-representation"></a>架構標記法

分析存放區中有兩種模式的結構描述標記法。 這些模式會在分欄式標記法的簡單性 (處理多型結構描述) 及查詢體驗的簡單性之間進行取捨：

* 妥善定義的架構標記法
* 完整精確度架構標記法

> [!NOTE]
> 針對 SQL (Core) API 帳戶，啟用分析存放區時，會妥善定義分析存放區中的預設架構標記法。 針對 Azure Cosmos DB MongoDB 帳戶的 API，分析存放區中的預設架構標記法是完整的架構標記法。 如果您的案例需要不同于這些 Api 預設供應專案的架構標記法，請與 [Azure Cosmos DB 小組](mailto:cosmosdbsynapselink@microsoft.com) 聯繫以加以啟用。

**妥善定義的架構標記法**

妥善定義的架構表示會在交易式存放區中建立架構無關資料的簡單表格式表示。 妥善定義的架構標記法有下列考慮：

* 屬性在多個專案之間一律具有相同的類型。

  * 例如，`{"a":123} {"a": "str"}` 沒有妥善定義的結構描述，因為 `"a"` 有時為字串，有時則為數字。 在此情況下，分析存放區會在容器的存留期內，將的資料類型註冊 `“a”` 為 `“a”` 第一個發生專案中的資料類型。 `“a”` 資料類型不同的項目將不會包含在分析存放區中。
  
    此狀況不適用於 Null 屬性。 例如， `{"a":123} {"a":null}` 仍然妥善定義。

* 陣列類型必須包含單一重複類型。

  * 例如，不是 `{"a": ["str",12]}` 妥善定義的架構，因為陣列包含整數和字串類型的混合。

> [!NOTE]
> 如果 Azure Cosmos DB 分析存放區遵循妥善定義的架構標記法，且特定專案違反上述規格，則這些專案將不會包含在分析存放區中。

**完整精確度架構標記法**

完整的精確度架構表示是設計用來處理架構中立運算元據中完整的多型架構。 在此架構標記法中，即使已定義妥善定義的架構條件約束 (沒有混合的資料類型欄位，或違反了混合的資料類型陣列) ，也不會從分析存放區卸載任何專案。

這是藉由根據屬性中值的資料類型，將運算元據的分葉屬性轉譯成具有不同資料行的分析存放區來達成。 分葉屬性名稱在分析存放區架構中會以資料類型做為尾碼來擴充，因此可以查詢而不會有混淆。

例如，讓我們在交易式存放區中採用下列範例檔：

```json
{
name: "John Doe",
age: 32,
profession: "Doctor",
address: {
  streetNo: 15850,
  streetName: "NE 40th St.",
  zip: 98052
},
salary: 1000000
}
```

`streetNo`嵌套物件內的分葉屬性 `address` 將以資料行表示在分析存放區架構中 `address.object.streetNo.int32` 。 資料類型會以後綴的形式加入資料行。 如此一來，如果另一份檔加入至交易式存放區，其中分葉屬性的值 `streetNo` 是 "123" (請注意，這是一個字串) ，分析存放區的架構會自動演進，而不會改變之前寫入資料行的類型。 新增至分析存放區的新資料行，其 `address.object.streetNo.string` 值為 "123" 的儲存位置。

**以後綴對應的資料類型**

以下是分析存放區中所有屬性資料類型及其後綴表示的對應：

|原始資料類型  |後置詞  |範例  |
|---------|---------|---------|
| Double |  "float64" |    24.99|
| Array | ". array" |    ["a"，"b"]|
|Binary | "binary" |0|
|Boolean    | "bool"   |True|
|Int32  | "int32"  |123|
|Int64  | "int64"  |255486129307|
|Null   | ". null"   | null|
|String|    "system.string" | "ABC"|
|時間戳記 |    ". timestamp" |  時間戳記 (0，0) |
|Datetime   |". date"    | ISODate ( "2020-08-21T07：43： 07.375 Z" ) |
|ObjectId   |"objectId"    | ObjectId ( "5f3f7b59330ec25c132623a2" ) |
|文件   |". object" |    {"a"： "a"}|

### <a name="cost-effective-archival-of-historical-data"></a>符合成本效益的歷程記錄資料封存

資料分層是指針對不同案例進行最佳化之儲存體基礎結構之間的資料分離。 因此可提升端對端資料堆疊的整體效能和成本效益。 Azure Cosmos DB 現在可透過分析存放區，支援從交易存放區將資料自動分層至具有不同資料配置的分析存放區。 相較於交易存放區，使用儲存成本最佳化的分析存放區可讓您保留更長的操作資料範圍來進行歷史分析。

啟用分析存放區之後，您可以根據交易工作負載的資料保留需求，設定「交易存放區存留時間 (交易 TTL)」屬性，以在一段時間後自動從交易存放區中刪除記錄。 同樣地，「分析存放區存留時間 (分析 TTL)」可讓您管理保留在與交易存放區無關之分析存放區中的資料生命週期。 您可以啟用分析存放區及設定 TTL 屬性，順暢地分層並定義兩個存放區的資料保留期間。

### <a name="global-distribution"></a>全域散發

如果您有全域散發的 Azure Cosmos DB 帳戶，則在啟用容器的分析存放區之後，該帳戶在所有區域都將可使用。  對操作資料所做的任何變更都會在所有區域中全域複寫。 您可以對 Azure Cosmos DB 中最近區域的資料複本，有效地執行分析查詢。

### <a name="security"></a>安全性

分析存放區的驗證與指定資料庫的交易存放區相同。 您可以使用主要或唯讀金鑰進行驗證。 您可以利用 Synapse Studio 中的連結服務，避免將 Azure Cosmos DB 金鑰貼入 Spark 筆記本中。 具有工作區存取權的任何人員都可以存取此連結服務。

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>支援多個 Azure Synapse Analytics 執行階段

分析存放區已最佳化，可提供分析工作負載的可擴縮性、彈性、效能，而不會對計算執行時間產生任何相關性。 儲存技術會進行自我管理，可將您的分析工作負載最佳化，而無須手動進行。

藉由將分析儲存系統與分析計算系統分離，可以從 Azure Synapse Analytics 所支援的不同分析執行階段同時查詢 Azure Cosmos DB 分析存放區中的資料。 即日起，Synapse Analytics 支援 Apache Spark 和 SQL 無伺服器，搭配 Azure Cosmos DB 分析存放區。

> [!NOTE]
> 您只能使用 Synapse Analytics 執行階段從分析存放區進行讀取。 您可以將資料寫回至交易存放區以做為服務層。

## <a name="pricing"></a><a id="analytical-store-pricing"></a> 定價

分析存放區會遵循以耗用量為基礎的定價模型，其中您需支付下列費用：

* 儲存體：每月保留在分析存放區中的資料量，包括分析 TTL 所定義的歷程記錄資料。

* 分析寫入作業：從交易存放區將完全管理的操作資料更新同步至分析存放區 (自動同步)

* 分析讀取作業：針對分析存放區，從 Synapse Analytics Spark 和 SQL 無伺服器執行時間執行的讀取作業。

> [!NOTE]
> Azure Cosmos DB 分析存放區目前免費提供公開預覽。

分析存放區定價與交易存放區定價模式不同。 分析存放區中沒有已佈建 RU 的概念。 如需分析存放區定價模式的完整詳細資料，請參閱 [Azure Cosmos DB 定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。

若要取得高階成本預估值，以在 Azure Cosmos DB 容器上啟用分析存放區，您可以使用 [Azure Cosmos DB 容量規劃工具](https://cosmos.azure.com/capacitycalculator/)，並取得分析儲存體和寫入作業成本的預估值。 分析讀取作業成本取決於分析工作負載的特性，但做為高階預估值，掃描分析存放區中 1 TB 的資料通常會產生 130,000 個分析讀取作業，並產生 $0.065 的成本。

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> 分析存留時間 (TTL)

分析 TTL 指出容器的資料應在分析存放區中保留的時間長度。 

如果已啟用分析存放區，則不論交易式 TTL 設定為何，運算元據的插入、更新、刪除作業都會自動從交易式存放區同步處理至分析存放區。 在分析存放區中，此操作資料的保留期可由容器層級的分析 TTL 值控制，如下所示：

容器上的分析 TTL 是使用 `AnalyticalStoreTimeToLiveInSeconds` 屬性進行設定：

* 如果值設定為 "0"，則遺漏 (或設為 Null)：停用分析存放區，且不會從交易存放區將任何資料複寫到分析存放區

* 如果存在且值設定為 "-1"：分析存放區會保留所有歷程記錄資料，而不論交易存放區中的資料保留期。 此設定表示分析存放區具有無限的操作資料保留期

* 如果存在且值設為某個正數 "n"：項目將會在交易存放區的上次修改時間 "n" 秒之後，在分析存放區中到期。 如果您想要在分析存放區中將運算元據保留一段有限的時間，不論交易式存放區中的資料保留期，都可以利用這項設定

考慮事項：

*   使用分析 TTL 值啟用分析存放區之後，稍後可將其更新為其他有效的值。 
*   雖然交易 TTL 可以在容器或項目層級設定，但分析 TTL 目前只能在容器層級設定。
*   您可以在容器層級設定分析 TTL >= 交易 TTL，從而在分析存放區中達到更長的操作資料保留期。
*   您可以設定分析 TTL = 交易式 TTL 來建立分析存放區，以鏡像交易式存放區。

當您在容器上啟用分析存放區時：

* 在 Azure 入口網站中，分析 TTL 選項會設定為預設值-1。 您可以藉由流覽至 [資料總管] 下的 [容器設定]，將此值變更為 [n] 秒。 
 
* 您可以從 Azure SDK 或 PowerShell 或 CLI 將 [分析 TTL] 選項設定為-1 或 ' n ' 來加以啟用。 

若要進一步了解，請參閱[如何在容器上設定分析 TTL](configure-synapse-link.md#create-analytical-ttl)。

## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱下列文件：

* [適用於 Azure Cosmos DB 的 Azure Synapse Link](synapse-link.md)

* [開始使用適用於 Azure Cosmos DB 的 Azure Synapse Link](configure-synapse-link.md)

* [關於適用於 Azure Cosmos DB 的 Synapse Link 常見問題](synapse-link-frequently-asked-questions.md)

* [適用於 Azure Cosmos DB 的 Azure Synapse Analytics 使用案例](synapse-link-use-cases.md)
