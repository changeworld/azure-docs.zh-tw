---
title: 適用於 Azure Cosmos DB 的 Azure Synapse Link、優點及使用時機
description: 瞭解適用於 Azure Cosmos DB 的 Azure Synapse Analytics。 Synapse Link 可讓您使用 Azure Synapse 分析，對 Azure Cosmos DB 中的操作資料執行近即時分析 (HTAP)。
author: Rodrigossz
ms.author: rosouz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.reviewer: sngun
ms.openlocfilehash: f200fe96478e15e938899d294ecd5491d6a03206
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814385"
---
# <a name="what-is-azure-synapse-link-for-azure-cosmos-db-preview"></a>什麼是適用於 Azure Cosmos DB 的 Azure Synapse Analytics (預覽)？

> [!IMPORTANT]
> 適用於 Azure Cosmos DB 的 Azure Synapse Analytics 目前為預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱[ Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

適用於 Azure Cosmos DB 的 Azure Synapse Analytics 是雲端原生的混合式交易和分析處理 (HTAP) 功能，可讓您對 Azure Cosmos DB 中的操作資料執行近即時分析。 Azure Synapse Link 會在 Azure Cosmos DB 與 Azure Synapse Analytics 之間建立緊密順暢的整合。

[Azure Cosmos DB 分析存放區](analytical-store-introduction.md)是完全隔離的資料行存放區，有了該存放區，Azure Synapse Link 可在 [Azure Synapse Analytics](../synapse-analytics/overview-what-is.md) 中對您的操作資料大規模進行無「擷取、轉換和下載」(ETL) 的分析。 商務分析師、資料工程師、資料科學家現在可以交換使用 Synapse Spark 或 Synapse SQL，執行近即時的商業智慧、分析和機器學習管線。 您可以達成此目的，且不會影響 Azure Cosmos DB 上的交易工作負載效能。 

下圖顯示 Azure Synapse Link 與 Azure Cosmos DB 和 Azure Synapse Analytics 的整合： 

:::image type="content" source="./media/synapse-link/synapse-analytics-cosmos-db-architecture.png" alt-text="Azure Synapse Analytics 與 Azure Cosmos DB 的整合架構圖" border="false":::

## <a name="benefits"></a><a id="synapse-link-benefits"></a> 優點

若要分析大型操作資料，同時將任務關鍵性交易工作負載的效能影響降到最低，傳統的做法是將 Azure Cosmos DB 的操作資料透過「擷取、轉換和下載」 (ETL) 管線進行擷取和處理。 ETL 管線需要多層的資料移動，因而導致作業的複雜性，並對交易工作負載的效能造成影響。 ETL 也會增加從來源到分析操作資料之間的時間延遲。

相較於傳統的 ETL 型解決方案，適用於 Azure Cosmos DB 的 AzureSynapse Link 提供了數個優點，例如：  

### <a name="reduced-complexity-with-no-etl-jobs-to-manage"></a>降低複雜性，無需管理 ETL 作業

Azure Synapse Link 可讓您使用 Azure Synapse Analytics 直接存取 Azure Cosmos DB 分析存放區，而不需要複雜的資料移動。 對操作資料所做的任何更新，會以近即時的方式反映在分析存放區中，不會有 ETL 或變更摘要作業。 您可以從 Synapse Analytics 對分析存放區執行大規模分析，而不需要額外的資料轉換。

### <a name="near-real-time-insights-into-your-operational-data"></a>操作資料的近即時深入解析

您現在可以使用 Azure Synapse Link 對您的操作資料取得近即時的豐富深入解析。 ETL 型的系統通常在分析操作資料時會有較高的延遲，因為需要擷取、轉換、下載操作資料這樣多層的作業。 藉由 Azure Cosmos DB 分析存放區與 Azure Synapse Analytics 的原生整合，您可以近即時分析操作資料，以實現新的商務案例。 


### <a name="no-impact-on-operational-workloads"></a>不影響作業的工作負載

使用 Azure Synapse Link，您可以對 Azure Cosmos DB 分析存放區 (分開的資料行存放區) 執行分析查詢，同時使用為交易工作負載佈建的輸送量 (以資料列為基礎的交易存放區) 來處理交易作業。  分析工作負載與交易工作負載流量無關，不會耗用針對操作資料所佈建的任何輸送量。

### <a name="optimized-for-large-scale-analytics-workloads"></a>已針對大規模的分析工作負載最佳化

Azure Cosmos DB 分析存放區已最佳化，可提供分析工作負載的可擴縮性、彈性、效能，而不會對計算執行時間產生任何相關性。 儲存技術會自我管理，以最佳化您的分析工作負載。 透過內建的 Azure Synapse Analytics 支援，存取此儲存層變得既簡單又高效能。

### <a name="cost-effective"></a>成本效益

有了 Azure Synapse Link，您可以使用成本最佳化且完全受控的解決方案來進行作業分析。 其可消除傳統 ETL 管線中用來分析操作資料所需的額外儲存和計算層。 

Azure Cosmos DB 分析存放區遵循耗用量定價模型，此模型是以資料儲存、分析讀取/寫入作業、執行的查詢為基礎。 您不需要佈建任何輸送量，如同您今天為交易工作負載所做的一樣。 使用 Azure Synapse Analytics 的高彈性計算引擎來存取資料，可讓執行儲存和計算的整體成本變得非常有效率。


### <a name="analytics-for-locally-available-globally-distributed-multi-master-data"></a>本機可用、全域散發、多重主機資料的分析

您可以對 Azure Cosmos DB 中最近區域的資料複本，有效地執行分析查詢。 Azure Cosmos DB 提供最先進的功能，以主動-主動的方式執行全域散發的分析工作負載以及交易工作負載。

## <a name="enable-htap-scenarios-for-your-operational-data"></a>為操作資料啟用 HTAP 案例

Synapse Link 會將 Azure Cosmos DB 分析存放區與 Azure Synapse Analytics 執行階段支援整合在一起。 此整合可讓您建立雲端原生的 HTAP (混合式交易/分析處理) 解決方案，根據大型資料集中的操作資料即時更新來產生深入解析。 於是帶來新的商務案例，可根據即時趨勢引發警示、建立近即時的儀表板、依據使用者行為的商業體驗。

### <a name="azure-cosmos-db-analytical-store"></a>Azure Cosmos DB 分析存放區

Azure Cosmos DB 分析存放區是 Azure Cosmos DB 中操作資料的資料行導向表示法。 此分析存放區適合用於大型操作資料的快速、符合成本效益的查詢，不需要複製資料，也不會影響交易工作負載的效能。

分析存放區會以近即時的方式，自動在您的交易工作負載中挑選高頻率的插入、更新、刪除，成為 Azure Cosmos DB 的完全受控功能 (「自動同步處理」)。 不需要變更摘要或 ETL。 

如果您有全域散發的 Azure Cosmos DB 帳戶，則在啟用容器的分析存放區之後，該帳戶在所有區域都將可加以使用。 如需分析存放區的詳細資訊，請參閱 [Azure Cosmos DB 分析存放區概觀](analytical-store-introduction.md)一文。

### <a name="integration-with-azure-synapse-analytics"></a><a id="synapse-link-integration"></a>與 Azure Synapse Analytics 整合

有了 Synapse Link，您現在可以從 Azure Synapse Analytics 直接連線到您的 Azure Cosmos DB 容器，並存取分析存放區，而不需要個別的連接器。 Azure Synapse Analytics 目前支援 [Synapse Apache Spark](../synapse-analytics/spark/apache-spark-concepts.md) 和 [Synapse SQL 無伺服器](../synapse-analytics/sql/on-demand-workspace-overview.md)的 Synapse 連結。

您可以同時查詢 Azure Cosmos DB 分析存放區中的資料，且 Azure Synapse Analytics 支援的不同分析執行之間可互通。 不需要進行其他資料轉換來分析操作資料。 您可以使用下列各項來查詢和分析分析存放區資料：

* 完全支援 Scala、Python、SparkSQL、C# 的 Synapse Apache Spark。 Synapse Spark 是資料工程和資料科學案例的核心

* SQL 無伺服器搭配 T-SQL 語言，並支援熟悉的 BI 工具 (例如 Power BI Premium 等)

> [!NOTE]
> 您可以從 Azure Synapse Analytics 存取 Azure Cosmos DB 容器中的分析和交易存放區。 不過，如果您想要對操作資料執行大規模分析或掃描，建議您使用分析存放區，以避免對交易工作負載的效能造成影響。

> [!NOTE]
> 您可以藉由將 Azure Cosmos DB 容器連接到該區域中的 Synapse 執行階段，在 Azure 區域中執行低延遲的分析。

這項整合可為不同的使用者提供下列 HTAP 案例：

* BI 工程師想要建立報表模型並發佈報表，以便直接透過 Synapse SQL 存取 Azure Cosmos DB 中的操作資料。

* 資料分析師想要從 Azure Cosmos DB 容器中的操作資料衍生深入解析，方法是透過使用 Synapse SQL 進行查詢、大規模讀取資料，並將這些結果與其他資料來源結合。

* 資料科學家想要使用 Synapse Spark 來尋找改善其模型的功能，並在不進行複雜資料工程的情況下定型該模型。 他們也可以將模型文章推斷的結果寫入 Azure Cosmos DB，以透過 Spark Synapse 對資料進行即時評分。

* 資料工程師想要讓取用者能夠存取資料，方法是在不需要手動 ETL 程序的情況下，透過 Azure Cosmos DB 容器建立 SQL 或 Spark 資料表。

如需有關 Azure Cosmos DB 的 Azure Synapse Analytics 執行階段支援的詳細資訊，請參閱 [Cosmos DB 的 Azure Synapse Analytics 支援](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)。

## <a name="when-to-use-azure-synapse-link-for-azure-cosmos-db"></a>何時使用適用於 Azure Cosmos DB 的 Azure Synapse Analytics？

在下列情況下，建議使用 Synapse Link：

* 如果您是 Azure Cosmos DB 客戶，而且想要對操作資料執行分析、BI 和機器學習服務。 在這種情況下，Synapse Link 會提供更整合的分析體驗，而不會影響交易存放區的佈建輸送量。 例如：

  * 如果您是在今天使用個別的連接器直接對 Azure Cosmos DB 操作資料執行分析或 BI，或者

  * 如果您執行 ETL 程序將操作資料擷取至不同的分析系統。
 
在這種情況下，Synapse Link 會提供更整合的分析體驗，而不會影響交易存放區的佈建輸送量。

如果您要尋找傳統的資料倉儲需求，例如高並行、工作負載管理、跨多個資料來源的彙總持續性，則不建議使用 Synapse Link。 如需詳細資訊，請參閱[適用於 Azure Cosmos DB 的 Azure Synapse Link 可支援的常見案例](synapse-link-use-cases.md)。


## <a name="limitations"></a>限制

* 在公開預覽期間中，只有 Azure Cosmos DB SQL (核心) API 支援 Synapse Link。 適用於 MongoDB 的 Azure Cosmos DB API 以及 Cassandra API 的支援目前處於有限預覽階段。 若要要求存取有限預覽，請寄電子郵件至 [Azure Cosmos DB 小組](mailto:cosmosdbsynapselink@microsoft.com)。

* 目前，只能為 (新的和現有的 Azure Cosmos DB 帳戶中) 新的容器啟用分析存放區。

* 在預覽中，針對已啟用 Synapse 連結的資料庫帳戶，不支援備份和還原容器。 如果您的生產工作負載需要備份和還原功能，建議您不要在這些資料庫帳戶上啟用 Synapse 連結。 

* 使用 Synapse SQL 無伺服器存取 Azure Cosmos DB 分析存放區目前處於有限預覽階段。 若要要求存取，請寄電子郵件至 [Azure Cosmos DB 小組](mailto:cosmosdbsynapselink@microsoft.com)。

* 目前無法使用已佈建的 Synapse SQL 存取 Azure Cosmos DB 分析存放區。

## <a name="pricing"></a>定價

Azure Synapse Link 的計費模型會包含使用 Azure Cosmos DB 分析存放區和 Synapse 執行階段所產生的成本。 若要深入瞭解，請參閱 [Azure Cosmos DB 分析存放區定價](analytical-store-introduction.md#analytical-store-pricing)和 [Azure Synapse Analytics 定價](https://azure.microsoft.com/pricing/details/synapse-analytics/)。

## <a name="next-steps"></a>後續步驟

若要深入了解，請參閱下列文件：

* [Azure Cosmos DB 分析存放區概觀](analytical-store-introduction.md)

* [開始使用適用於 Azure Cosmos DB 的 Azure Synapse Link](configure-synapse-link.md)
 
* [Azure Synapse Analytics 執行階段支援的功能](../synapse-analytics/synapse-link/concept-synapse-link-cosmos-db-support.md)

* [關於適用於 Azure Cosmos DB 的 Azure Synapse Link 常見問題](synapse-link-frequently-asked-questions.md)

* [適用於 Azure Cosmos DB 的 Azure Synapse Analytics 使用案例](synapse-link-use-cases.md)
