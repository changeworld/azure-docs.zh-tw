---
title: 在 Azure Cosmos 容器和資料庫上佈建輸送量
description: 了解如何設定 Azure Cosmos 容器和資料庫的佈建輸送量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 910a0d9b70a63fc93aebd47896db7c3493c846b2
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684027"
---
# <a name="introduction-to-provisioned-throughput-in-azure-cosmos-db"></a>簡介 Azure Cosmos DB 中的佈建輸送量

Azure Cosmos DB 可讓您在資料庫和容器上設定佈建輸送量。 佈建輸送量有兩種類型：標準 (手動) 或自動調整。 本文概述佈建輸送量如何運作。 

Azure Cosmos 資料庫是一組容器的管理單位。 資料庫是由一組無從驗證結構描述的容器所組成的。 Azure Cosmos 容器是適用於輸送量和儲存體的延展性單位。 容器會跨 Azure 區域內的一組機器進行水平分割，並散發到與您 Azure Cosmos 帳戶相關聯的所有 Azure 區域。

使用 Azure Cosmos DB，您可以佈建兩個資料粒度的輸送量：
 
- Azure Cosmos 容器
- Azure Cosmos 資料庫

## <a name="set-throughput-on-a-container"></a>在容器上設定輸送量  

在 Azure Cosmos 容器上佈建的輸送量是專為該容器保留的。 該容器隨時都可接收佈建輸送量。 容器上的佈建輸送量在財務方面會由 SLA 所支援。 若要了解如何在容器上設定標準 (手動) 輸送量，請參閱[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。 若要了解如何在容器上設定自動調整輸送量，請參閱[佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。

在容器上設定佈建的輸送量是最常使用的選項。 您可以透過使用[要求單位 (RU)](request-units.md) 佈建任何數量的輸送量，來彈性擴充容器的輸送量。 

針對容器佈建的輸送量會平均分散於其實體分割區之間，並假設有一個良好的分割區索引鍵，可在實體分割區之間平均分散邏輯分割區，則輸送量也會平均分散於容器的所有邏輯分割區之間。 您無法選擇性地指定邏輯分割區的輸送量。 因為實體分割區會裝載容器的一或多個邏輯分割區；因此，實體分割區專屬於該容器並支援該容器上佈建的輸送量。 

如果邏輯分割區上執行的工作負載所取用的輸送量超過配置給該邏輯分割區的輸送量時，您的作業會受到速率限制。 發生速率限制時，您可以提高整個容器的佈建輸送量或重試此作業。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

當您想要保證容器的效能時，我們建議您在容器資料粒度上設定輸送量。

下圖示範實體分割區如何裝載容器的一或多個邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>在資料庫上設定輸送量

當您在 Azure Cosmos 資料庫上佈建輸送量時，輸送量會在資料庫中的所有容器 (稱為標準資料庫容器) 上共用。 例外狀況是您在資料庫中特定的容器上指定了佈建輸送量。 在其容器之間共用資料庫層級佈建輸送量，相當於在機器叢集上裝載資料庫。 因為資料庫共用內的所有容器都會共用機器上可用的資源，因此，您自然不會取得任何特定容器上的可預測效能。 若要了解如何在資料庫上設定佈建輸送量，請參閱[在 Azure Cosmos 資料庫上設定佈建輸送量](how-to-provision-database-throughput.md)。 若要了解如何在資料上設定自動調整輸送量，請參閱[佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。

在 Azure Cosmos 資料庫上設定輸送量，保證您隨時都能接收到該資料庫的佈建輸送量。 因為資料庫共用內的所有容器都會共用佈建輸送量，因此，Azure Cosmos DB 不會針對該資料庫中的特定容器提供任何可預測的輸送量保證。 特定容器可接收的輸送量部分取決於：

* 容器數目。
* 對於各種容器所選擇的分割區索引鍵。
* 散發於容器中各種不同邏輯分割區的工作負載。 

當您想要在多個容器之間共用輸送量，但不想讓輸送量專屬於任何特定容器時，我們建議您在資料庫上設定輸送量。 

以下是最好在資料庫層級佈建輸送量的範例：

* 在一組容器上共用資料庫的佈建輸送量非常適用於多租用戶應用程式。 每位使用者都可透過不同的 Azure Cosmos 容器來表示。

* 當您將從 VM 叢集或從內部部署實體伺服器裝載的 NoSQL 資料庫 (例如 MongoDB 或 Cassandra) 移轉到 Azure Cosmos DB 時，在一組容器之間共用資料庫的佈建輸送量就很實用。 將 Azure Cosmos 資料庫上設定的佈建輸送量想像為 MongoDB 或 Cassandra 叢集計算容量之佈建輸送量的邏輯對等項目 (但更符合成本效益且更具彈性)。  

以所佈建輸送量在資料庫內建立的所有容器，在建立時都必須具有[分割區索引鍵](partition-data.md)。 在任何指定的時間點，配置給資料庫內容器的輸送量都會散發到該容器的所有邏輯分割區。 當您有容器共用資料庫上設定的佈建輸送量時，就無法選擇性地將輸送量套用到特定容器或邏輯分割區。 

如果邏輯分割區上的工作負載所取用的輸送量超過配置給特定邏輯分割區的輸送量時，您的作業將會受到速率限制。 發生速率限制時，您可以提高整個資料庫的輸送量或重試此作業。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

共用輸送量資料庫中的容器會共用配置給該資料庫的輸送量 (RU/秒)。 在資料庫中，您最多可以有四個最小輸送量為 400 RU/秒的容器。 使用標準 (手動) 佈建輸送量時，前四個容器之後的每個新容器最少都需要額外的 100 RU/秒。 例如，如果您有包含八個容器的共用輸送量資料庫，資料庫中的最低值 (RU/秒) 將是 800 RU/秒。 使用自動調整佈建輸送量時，資料庫中容器的自動調整 RU/秒上限為 4000 RU/秒 (在 400-4000 RU/秒之間調整)。

> [!NOTE]
> 我們在 2020 年 2 月引進了一項變更，可讓您在共用的輸送量資料庫中最多擁有 25 個容器，讓輸送量更能在容器之間共用。 在前 25 個容器之後，您就可以新增更多容器至資料庫，唯一前提是其是[使用專用輸送量佈建的](#set-throughput-on-a-database-and-a-container)，這與資料庫的共用輸送量不同。<br>
如果您的 Azure Cosmos DB 帳戶已經包含具有 >= 25 個容器的共用輸送量資料庫，則該帳戶與相同 Azure 訂用帳戶中的其他所有帳戶都無法豁免於這項變更。 如果您有任何意見反應或問題，請[連絡產品支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果您的工作負載涉及刪除和重新建立資料庫中的所有集合，建議您先捨棄空的資料庫並重新建立新的資料庫，然後再建立集合。 下圖顯示實體分割區如何裝載一或多個屬於資料庫內不同容器的邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>在資料庫和容器上設定輸送量

您可以結合這兩個模型， 就能同時在資料庫和容器上佈建輸送量。 下列範例說明如何在 Azure Cosmos 資料庫和容器上佈建標準(手動) 佈建輸送量：

* 您可以使用 *"K"* 個 RU 的標準 (手動) 佈建輸送量來建立名為 *Z* 的 Azure Cosmos 資料庫。 
* 接下來，在資料庫中建立五個容器，名稱分別為 *A*、*B*、*C*、*D* 和 *E*。 建立容器 B 時，請務必啟用 [為此容器佈建專用輸送量] 選項，並在此容器上明確設定 *"P"* 個 RU 的佈建輸送量。 請注意，只有在建立資料庫和容器時，才能設定共用和專用輸送量。 

   ![設定容器層級的輸送量](./media/set-throughput/coll-level-throughput.png)

* *"K"* RU 輸送量會在四個容器 *A*、*C*、*D* 和 *E*之間共用。*A*、*C*、*D* 或 *E* 可用的確切輸送量數量會有所不同。 沒有適用於每個個別容器輸送量的 SLA。
* 容器 *B* 保證能夠隨時取得 *"P"* 個 RU 的輸送量， 並受到 SLA 支援。

> [!NOTE]
> 具有佈建輸送量的容器無法轉換為共用資料庫容器。 反之，共用資料庫容器也無法轉換為具有專用輸送量。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新資料庫和容器上的輸送量

在建立 Azure Cosmos 容器或資料庫之後，您可以更新佈建輸送量。 您可以在資料庫或容器上設定的最大佈建輸送量沒有任何限制。 [最小佈建輸送量](concepts-limits.md#storage-and-throughput)取決於下列因素： 

* 您在容器中儲存的目前資料大小
* 您曾在容器上佈建的最大輸送量
* 在具有共用輸送量的資料庫中，您目前具有的 Azure Cosmos 容器數量。 

您可以使用 SDK，以程式設計方式擷取容器或資料庫的最小輸送量，或檢視 Azure 入口網站中的值。 使用 .NET SDK 時，[DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) 方法可讓您調整佈建輸送量的值。 使用 Java SDK 時，[RequestOptions.setOfferThroughput](sql-api-java-sdk-samples.md) 方法可讓您調整佈建輸送量的值。 

使用 .NET SDK 時，[DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) 方法可讓您擷取容器或資料庫的最小輸送量。 

您可以隨時調整容器或資料庫的佈建輸送量。 執行調整作業以增加輸送量時，可能需要較長的時間，因為系統工作會佈建所需的資源。 您可以在 Azure 入口網站中，或使用 SDK 以程式設計方式檢查調整作業的狀態。 使用 .NET SDK 時，您可以使用 `DocumentClient.ReadOfferAsync` 方法來取得調整作業的狀態。

## <a name="comparison-of-models"></a>模型的比較
下表顯示在資料庫上與在容器上佈建標準 (手動) 輸送量之間的比較。 

|**參數**  |**資料庫上的標準 (手動) 輸送量**  |**容器上的標準 (手動) 輸送量**|**資料庫上的自動調整輸送量** | **容器上的自動調整輸送量**|
|---------|---------|---------|---------|---------|
|進入點 (最小 RU/秒) |400 RU/秒。 在前四個容器之後，每個額外的容器至少需要 100 RU/秒</li> |400| 在 400 - 4000 RU/秒之間自動調整。 最多可有 25 個容器，每個容器沒有最小 RU/秒</li> | 在 400 - 4000 RU/秒之間自動調整。|
|每個容器的最小 RU/秒|100|400|--|在 400 - 4000 RU/秒之間自動調整|
|RU 數目上限|在資料庫上無限制。|在容器上無限制。|在資料庫上無限制。|在容器上無限制。
|指派給或適用於特定容器的 RU|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|
|容器的儲存體上限|無限制。|無限制|無限制|無限制|
|容器中每個邏輯分割區的輸送量上限|10K RU/秒|10K RU/秒|10K RU/秒|10K RU/秒|
|容器中每個邏輯分割區的儲存體上限 (資料 + 索引)|20 GB|20 GB|20 GB|20 GB|

## <a name="next-steps"></a>後續步驟

* 深入了解[邏輯分割區](partition-data.md)。
* 了解如何[在 Azure Cosmos 容器上佈建標準 (手動) 輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建標準 (手動) 輸送量](how-to-provision-database-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫或容器上佈建自動調整輸送量](how-to-provision-autoscale-throughput.md)。
