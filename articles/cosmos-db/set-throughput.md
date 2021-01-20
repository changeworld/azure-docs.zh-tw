---
title: 在 Azure Cosmos 容器和資料庫上佈建輸送量
description: 了解如何設定 Azure Cosmos 容器和資料庫的佈建輸送量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: a03ad1eb893c97671d7ab60cc38708115a73d260
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602400"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>簡介 Azure Cosmos DB 中的佈建輸送量
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 可讓您在資料庫和容器上設定佈建輸送量。 佈建輸送量有兩種類型：標準 (手動) 或自動調整。 本文概述布建的輸送量的運作方式。 

Azure Cosmos 資料庫是一組容器的管理單位。 資料庫是由一組無從驗證結構描述的容器所組成的。 Azure Cosmos 容器是適用於輸送量和儲存體的延展性單位。 容器會跨 Azure 區域內的一組機器進行水平分割，並散發到與您 Azure Cosmos 帳戶相關聯的所有 Azure 區域。

使用 Azure Cosmos DB，您可以佈建兩個資料粒度的輸送量：
 
- Azure Cosmos 容器
- Azure Cosmos 資料庫

## <a name="set-throughput-on-a-container"></a>在容器上設定輸送量  

在 Azure Cosmos 容器上佈建的輸送量是專為該容器保留的。 該容器隨時都可接收佈建輸送量。 容器上的佈建輸送量在財務方面會由 SLA 所支援。 若要了解如何在容器上設定標準 (手動) 輸送量，請參閱[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。 若要了解如何在容器上設定自動調整輸送量，請參閱[佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。

在容器上設定佈建的輸送量是最常使用的選項。 您可以透過使用[要求單位 (RU)](request-units.md) 佈建任何數量的輸送量，來彈性擴充容器的輸送量。 

針對容器佈建的輸送量會平均分散於其實體分割區之間，並假設有一個良好的分割區索引鍵，可在實體分割區之間平均分散邏輯分割區，則輸送量也會平均分散於容器的所有邏輯分割區之間。 您無法選擇性地指定邏輯分割區的輸送量。 因為實體分割區會裝載容器的一或多個邏輯分割區；因此，實體分割區專屬於該容器並支援該容器上佈建的輸送量。 

如果邏輯分割區上執行的工作負載耗用的輸送量超過配置給基礎實體分割區的輸送量，您的作業可能會有速率限制。 當某個邏輯分割區的要求數目比其他資料分割索引鍵值還多時，就會發生所謂的 _熱分割_ 。

發生速率限制時，您可以提高整個容器的佈建輸送量或重試此作業。 您也應該確定您選擇的是平均分配儲存體和要求磁片區的分割區索引鍵。 如需有關資料分割的詳細資訊，請參閱 [Azure Cosmos DB 中的資料分割和水準調整](partitioning-overview.md)。

當您想要容器的可預測效能時，建議您在容器資料細微性上設定輸送量。

下圖示範實體分割區如何裝載容器的一或多個邏輯分割區：

:::image type="content" source="./media/set-throughput/resource-partition.png" alt-text="裝載容器的一或多個邏輯分割區的實體分割區" border="false":::

## <a name="set-throughput-on-a-database"></a>在資料庫上設定輸送量

當您在 Azure Cosmos 資料庫上佈建輸送量時，輸送量會在資料庫中的所有容器 (稱為標準資料庫容器) 上共用。 例外狀況是您在資料庫中特定的容器上指定了佈建輸送量。 在其容器之間共用資料庫層級佈建輸送量，相當於在機器叢集上裝載資料庫。 因為資料庫共用內的所有容器都會共用機器上可用的資源，因此，您自然不會取得任何特定容器上的可預測效能。 若要了解如何在資料庫上設定佈建輸送量，請參閱[在 Azure Cosmos 資料庫上設定佈建輸送量](how-to-provision-database-throughput.md)。 若要了解如何在資料上設定自動調整輸送量，請參閱[佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。

因為資料庫共用內的所有容器都會共用佈建輸送量，因此，Azure Cosmos DB 不會針對該資料庫中的特定容器提供任何可預測的輸送量保證。 特定容器可接收的輸送量部分取決於：

* 容器數目。
* 對於各種容器所選擇的分割區索引鍵。
* 散發於容器中各種不同邏輯分割區的工作負載。 

當您想要在多個容器之間共用輸送量，但不想讓輸送量專屬於任何特定容器時，我們建議您在資料庫上設定輸送量。 

以下是最好在資料庫層級佈建輸送量的範例：

* 在一組容器上共用資料庫的佈建輸送量非常適用於多租用戶應用程式。 每位使用者都可透過不同的 Azure Cosmos 容器來表示。

* 當您將從 VM 叢集或從內部部署實體伺服器裝載的 NoSQL 資料庫 (例如 MongoDB 或 Cassandra) 移轉到 Azure Cosmos DB 時，在一組容器之間共用資料庫的佈建輸送量就很實用。 將 Azure Cosmos 資料庫上設定的佈建輸送量想像為 MongoDB 或 Cassandra 叢集計算容量之佈建輸送量的邏輯對等項目 (但更符合成本效益且更具彈性)。  

以所佈建輸送量在資料庫內建立的所有容器，在建立時都必須具有[分割區索引鍵](partitioning-overview.md)。 在任何指定的時間點，配置給資料庫內容器的輸送量都會散發到該容器的所有邏輯分割區。 當您有容器共用資料庫上設定的佈建輸送量時，就無法選擇性地將輸送量套用到特定容器或邏輯分割區。 

如果邏輯分割區上的工作負載所取用的輸送量超過配置給特定邏輯分割區的輸送量時，您的作業將會受到速率限制。 發生速率限制時，您可以提高整個資料庫的輸送量或重試此作業。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partitioning-overview.md)。

共用輸送量資料庫中的容器會共用配置給該資料庫的輸送量 (RU/秒)。 使用標準 (手動) 布建的輸送量，在資料庫上最多可以有25個容器，且最多可有 400 RU/秒。 透過自動調整布建的輸送量，資料庫中最多可以有25個容器，其中最多可有 4000 RU/秒的自動調整， (在 400-4000 RU/秒) 之間進行調整。

> [!NOTE]
> 我們在 2020 年 2 月引進了一項變更，可讓您在共用的輸送量資料庫中最多擁有 25 個容器，讓輸送量更能在容器之間共用。 在前 25 個容器之後，您就可以新增更多容器至資料庫，唯一前提是其是[使用專用輸送量佈建的](#set-throughput-on-a-database-and-a-container)，這與資料庫的共用輸送量不同。<br>
如果您的 Azure Cosmos DB 帳戶已經包含具有 >= 25 個容器的共用輸送量資料庫，則該帳戶與相同 Azure 訂用帳戶中的其他所有帳戶都無法豁免於這項變更。 如果您有任何意見反應或問題，請[連絡產品支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果您的工作負載涉及刪除和重新建立資料庫中的所有集合，建議您先捨棄空的資料庫並重新建立新的資料庫，然後再建立集合。 下圖顯示實體分割區如何裝載一或多個屬於資料庫內不同容器的邏輯分割區：

:::image type="content" source="./media/set-throughput/resource-partition2.png" alt-text="裝載屬於不同容器的一或多個邏輯分割區的實體分割區 " border="false":::

## <a name="set-throughput-on-a-database-and-a-container"></a>在資料庫和容器上設定輸送量

您可以結合這兩個模型， 就能同時在資料庫和容器上佈建輸送量。 下列範例說明如何在 Azure Cosmos 資料庫和容器上佈建標準(手動) 佈建輸送量：

* 您可以使用 *"K"* 個 RU 的標準 (手動) 佈建輸送量來建立名為 *Z* 的 Azure Cosmos 資料庫。 
* 接下來，在資料庫中建立五個容器，名稱分別為 *A*、*B*、*C*、*D* 和 *E*。 建立容器 B 時，請務必啟用 [為此容器佈建專用輸送量] 選項，並在此容器上明確設定 *"P"* 個 RU 的佈建輸送量。 只有在建立資料庫和容器時，您才能設定共用和專用輸送量。 

   :::image type="content" source="./media/set-throughput/coll-level-throughput.png" alt-text="設定容器層級的輸送量":::

* *"K"* RU 輸送量會在四個容器 *A*、*C*、*D* 和 *E* 之間共用。*A*、*C*、*D* 或 *E* 可用的確切輸送量數量會有所不同。 沒有適用於每個個別容器輸送量的 SLA。
* 容器 *B* 保證能夠隨時取得 *"P"* 個 RU 的輸送量， 並受到 SLA 支援。

> [!NOTE]
> 具有佈建輸送量的容器無法轉換為共用資料庫容器。 反之，共用資料庫容器也無法轉換為具有專用輸送量。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新資料庫和容器上的輸送量

在建立 Azure Cosmos 容器或資料庫之後，您可以更新佈建輸送量。 您可以在資料庫或容器上設定的最大佈建輸送量沒有任何限制。

### <a name="current-provisioned-throughput"></a><a id="current-provisioned-throughput"></a> 目前布建的輸送量

您可以在 Azure 入口網站中或使用 Sdk 來取得容器或資料庫的布建輸送量：

* .NET SDK 上的[ReadThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.readthroughputasync?view=azure-dotnet&preserve-view=true) 。
* JAVA SDK 上的[CosmosContainer. readThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.readthroughput?view=azure-java-stable&preserve-view=true) 。

這些方法的回應也包含容器或資料庫的 [最小布建輸送量](concepts-limits.md#storage-and-database-operations) ：

* .NET SDK 上的[ThroughputResponse. MinThroughput](/dotnet/api/microsoft.azure.cosmos.throughputresponse.minthroughput?view=azure-dotnet&preserve-view=true) 。
* [ThroughputResponse. getMinThroughput ( ](/java/api/com.azure.cosmos.models.throughputresponse.getminthroughput?view=azure-java-stable&preserve-view=true) JAVA SDK 上的 # B1。

實際的最小 RU/秒可能會依您的帳戶設定而有所不同。 但一般而言，它的最大值是：

* 400 RU/秒 
* 目前的儲存體（GB） * 10 RU/秒 (此條件約束在某些情況下可能會放寬，請參閱我們的 [高儲存體/低輸送量程式](#high-storage-low-throughput-program)) 
* 在資料庫或容器上布建的最高 RU/秒/100

### <a name="changing-the-provisioned-throughput"></a>變更布建的輸送量

您可以透過 Azure 入口網站或使用 Sdk 來調整容器或資料庫的布建輸送量：

* .NET SDK 上的[ReplaceThroughputAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet&preserve-view=true) 。
* JAVA SDK 上的[CosmosContainer. replaceThroughput](/java/api/com.azure.cosmos.cosmosasynccontainer.replacethroughput?view=azure-java-stable&preserve-view=true) 。

如果您要 **減少布建的輸送量**，您將能夠達到 [最小值](#current-provisioned-throughput)。

如果您要 **增加布建的輸送量**，大部分的情況下，作業會立即進行。 不過，在某些情況下，作業可能需要較長的時間，因為系統工作會布建所需的資源。 在此情況下，嘗試在此作業進行時修改布建的輸送量將會產生 HTTP 423 回應，並顯示錯誤訊息，說明另一個調整作業正在進行中。

> [!NOTE]
> 如果您正在規劃非常大型的內嵌工作負載，且需要大幅增加布建的輸送量，請記住調整規模作業沒有 SLA，而在上一段所述的情況下，可能會花很長的時間來提高規模。 您可能想要在工作負載啟動之前事先規劃並開始調整，並使用下列方法來檢查進度。

您可以藉由讀取 [目前布建的輸送量](#current-provisioned-throughput) ，並使用下列方法，以程式設計方式檢查調整進度：

* .NET SDK 上的[ThroughputResponse. IsReplacePending](/dotnet/api/microsoft.azure.cosmos.throughputresponse.isreplacepending?view=azure-dotnet&preserve-view=true) 。
* [ThroughputResponse. isReplacePending ( ](/java/api/com.azure.cosmos.models.throughputresponse.isreplacepending?view=azure-java-stable&preserve-view=true) JAVA SDK 上的 # B1。

您可以使用 [Azure 監視器計量](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) 來查看布建輸送量的歷程記錄 (RU/秒) 和資源上的儲存體。

## <a name="high-storage--low-throughput-program"></a><a id="high-storage-low-throughput-program"></a> 高儲存體/低輸送量程式

如上面目前布 [建的輸送量](#current-provisioned-throughput) 一節所述，您可以在容器或資料庫上布建的最小輸送量取決於許多因素。 其中一個是目前儲存的資料量，因為 Azure Cosmos DB 強制執行每 GB 儲存體 10 RU/秒的最小輸送量。

這可能會在您需要儲存大量資料，但比較中有低輸送量需求的情況下考慮。 為了更妥善配合這些案例，Azure Cosmos DB 引進了「 **高儲存體/低輸送量」程式** ，可減少符合資格之帳戶的每 GB RU/秒。

若要加入此計畫並評估您的完整資格，您只需要填寫 [這份問卷](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRzBPrdEMjvxPuDm8fCLUtXpUREdDU0pCR0lVVFY5T1lRVEhWNUZITUJGMC4u)。 然後，Azure Cosmos DB 的小組會跟進並繼續進行上線。

## <a name="comparison-of-models"></a>模型的比較
下表顯示在資料庫上與在容器上佈建標準 (手動) 輸送量之間的比較。 

|**參數**  |**資料庫上的標準 (手動) 輸送量**  |**容器上的標準 (手動) 輸送量**|**資料庫上的自動調整輸送量** | **容器上的自動調整輸送量**|
|---------|---------|---------|---------|---------|
|進入點 (最小 RU/秒) |400 RU/秒。 每個容器最多可以有25個容器，每個容器都沒有 RU/秒。</li> |400| 在 400 - 4000 RU/秒之間自動調整。 每個容器最多可以有25個容器，每個容器都沒有 RU/秒。</li> | 在 400 - 4000 RU/秒之間自動調整。|
|每個容器的最小 RU/秒|--|400|--|在 400 - 4000 RU/秒之間自動調整|
|RU 數目上限|在資料庫上無限制。|在容器上無限制。|在資料庫上無限制。|在容器上無限制。
|指派給或適用於特定容器的 RU|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|
|容器的儲存體上限|無限制。|無限制|無限制|無限制|
|容器中每個邏輯分割區的輸送量上限|10K RU/秒|10K RU/秒|10K RU/秒|10K RU/秒|
|容器中每個邏輯分割區的儲存體上限 (資料 + 索引)|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>後續步驟

* 深入了解[邏輯分割區](partitioning-overview.md)。
* 了解如何[在 Azure Cosmos 容器上佈建標準 (手動) 輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建標準 (手動) 輸送量](how-to-provision-database-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫或容器上佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。
