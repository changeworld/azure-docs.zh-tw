---
title: 在 Azure Cosmos 容器和資料庫上佈建輸送量
description: 了解如何設定 Azure Cosmos 容器和資料庫的佈建輸送量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251969"
---
# <a name="provision-throughput-on-containers-and-databases"></a>在容器和資料庫中佈建輸送量

Azure Cosmos 資料庫是一組容器的管理單位。 資料庫是由一組無從驗證結構描述的容器所組成的。 Azure Cosmos 容器是適用於輸送量和儲存體的延展性單位。 容器會跨 Azure 區域內的一組機器進行水平分割，並散發到與您 Azure Cosmos 帳戶相關聯的所有 Azure 區域。

使用 Azure Cosmos DB，可以預配輸送量，但細微性為兩個細微性：
 
- Azure Cosmos 容器
- Azure Cosmos 資料庫

## <a name="set-throughput-on-a-container"></a>在容器上設定輸送量  

Azure Cosmos 容器上預配的輸送量僅保留給該容器。 該容器隨時都可接收佈建輸送量。 容器上的佈建輸送量在財務方面會由 SLA 所支援。 要瞭解如何配置容器上的輸送量，請參閱 Azure [Cosmos 容器上的預配輸送量](how-to-provision-container-throughput.md)。

在容器上設置預配輸送量是最常用的選項。 通過使用[請求單位 （R）](request-units.md)預配任意數量的輸送量，可以彈性擴展容器的輸送量。 

為容器預配的輸送量在其物理分區之間均勻分佈，假設良好的分區鍵在物理分區之間均勻分佈邏輯分區，輸送量也會均勻分佈于所有分區中容器的邏輯分區。 不能有選擇地指定邏輯分區的輸送量。 因為實體分割區會裝載容器的一或多個邏輯分割區；因此，實體分割區專屬於該容器並支援該容器上佈建的輸送量。 

如果在邏輯分區上運行的工作負載消耗的輸送量超過分配給該邏輯分區的輸送量，則操作將受速率限制。 發生速率限制時，可以增加整個容器的預配輸送量，也可以重試操作。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

當您想要保證容器的效能時，我們建議您在容器資料粒度上設定輸送量。

下圖示範實體分割區如何裝載容器的一或多個邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>在資料庫上設定輸送量

在 Azure Cosmos 資料庫上預配輸送量時，輸送量將共用到資料庫中的所有容器（稱為共用資料庫容器）。 如果為資料庫中的特定容器指定了預配輸送量，則有一個例外。 在其容器之間共用資料庫級預配輸送量類似于在電腦群集上託管資料庫。 因為資料庫共用內的所有容器都會共用機器上可用的資源，因此，您自然不會取得任何特定容器上的可預測效能。 要瞭解如何在資料庫上配置預配輸送量，請參閱[在 Azure Cosmos 資料庫上配置預配輸送量](how-to-provision-database-throughput.md)。

在 Azure Cosmos 資料庫上設置輸送量可確保您一直接收該資料庫的預配輸送量。 因為資料庫共用內的所有容器都會共用佈建輸送量，因此，Azure Cosmos DB 不會針對該資料庫中的特定容器提供任何可預測的輸送量保證。 特定容器可接收的輸送量部分取決於：

* 容器數目。
* 對於各種容器所選擇的分割區索引鍵。
* 散發於容器中各種不同邏輯分割區的工作負載。 

當您想要在多個容器之間共用輸送量，但不想讓輸送量專屬於任何特定容器時，我們建議您在資料庫上設定輸送量。 

以下是最好在資料庫層級佈建輸送量的範例：

* 在一組容器上共用資料庫的佈建輸送量非常適用於多租用戶應用程式。 每位使用者都可透過不同的 Azure Cosmos 容器來表示。

* 當您將從 VM 叢集或從內部部署實體伺服器裝載的 NoSQL 資料庫 (例如 MongoDB 或 Cassandra) 移轉到 Azure Cosmos DB 時，在一組容器之間共用資料庫的佈建輸送量就很實用。 將 Azure Cosmos 資料庫上設定的佈建輸送量想像為 MongoDB 或 Cassandra 叢集計算容量之佈建輸送量的邏輯對等項目 (但更符合成本效益且更具彈性)。  

必須在具有預配輸送量的資料庫內創建的所有容器都必須使用[分區鍵](partition-data.md)創建。 在任何給定時間點，分配給資料庫中容器的輸送量都分佈在該容器的所有邏輯分區中。 在資料庫上配置了共用預配輸送量的容器時，無法有選擇地將輸送量應用於特定容器或邏輯分區。 

如果邏輯分割區上的工作負載所取用的輸送量超過配置給特定邏輯分割區的輸送量時，您的作業將會受到速率限制。 發生速率限制時，可以增加整個資料庫的輸送量或重試操作。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

共用輸送量資料庫中的容器共用分配給該資料庫的輸送量 （RU/s）。 資料庫中最多隻能有四個容器，至少 400 RU/s。 前四個容器之後的每個新容器將需要額外的 100 RU/s。 例如，如果您有一個包含八個容器的共用輸送量資料庫，則資料庫上的最小 RU/s 將為 800 RU/s。

> [!NOTE]
> 2020 年 2 月，我們引入了一項更改，允許您在共用輸送量資料庫中最多包含 25 個容器，從而更好地跨容器共用輸送量。 在前 25 個容器之後，只有在使用[專用輸送量（](#set-throughput-on-a-database-and-a-container)與資料庫的共用輸送量分開）預配容器時，才能向資料庫添加更多容器。<br>
如果 Azure Cosmos DB 帳戶已包含包含>25 個容器的共用輸送量資料庫，則該帳戶和同一 Azure 訂閱中的所有其他帳戶將免于此更改。 如果您有回饋或問題[，請聯繫產品支援](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。 

如果工作負荷涉及刪除和重新創建資料庫中的所有集合，建議您在創建集合之前刪除空資料庫並重新創建新資料庫。 下圖顯示實體分割區如何裝載一或多個屬於資料庫內不同容器的邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>在資料庫和容器上設定輸送量

您可以結合這兩個模型， 就能同時在資料庫和容器上佈建輸送量。 下列範例說明如何在 Azure Cosmos 資料庫和容器上佈建輸送量：

* 您可以創建名為*Z*的 Azure Cosmos 資料庫，其預配輸送量為 *"K"R。* 
* 接下來，*E*在資料庫中創建名為*A*A、B、C、D 和 E 的五個容器。 *B* *C* *D* 創建容器 B 時，請確保**為此容器選項啟用預配專用輸送量**，並在此容器上顯式配置預配輸送量的 *"P"R。* 請注意，只有在創建資料庫和容器時，才能配置共用輸送量和專用輸送量。 

   ![在容器級別設置輸送量](./media/set-throughput/coll-level-throughput.png)

* *"K"R*魯斯輸送量在四個容器*A*A、C、D*D*和*E*之間共用。 *C**E* *A、C、D*或*A*E 可用的*D*輸送量的確切量數各不相同。 而且沒有適用於每個個別容器輸送量的 SLA。
* 容器名為*B*保證一直獲得 *"P"RA*輸送量。 並受到 SLA 支援。

> [!NOTE]
> 無法將具有預配輸送量的容器轉換為共用資料庫容器。 相反，無法將共用資料庫容器轉換為具有專用輸送量。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新資料庫或容器上的輸送量

創建 Azure Cosmos 容器或資料庫後，可以更新預配輸送量。 可以在資料庫或容器上配置的最大預配輸送量沒有限制。 [最小預配輸送量](concepts-limits.md#storage-and-throughput)取決於以下因素： 

* 存儲在容器中的最大資料大小
* 在容器上預配的最大輸送量
* 資料庫中具有共用輸送量的 Azure Cosmos 容器的當前數量。 

可以使用 SDK 或在 Azure 門戶中查看該值，以程式設計方式檢索容器或資料庫的最低輸送量。 使用 .NET SDK 時[，DocumentClient.replaceAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet)方法允許您縮放預配輸送量值。 使用 JAVA SDK 時[，RequestOptions.setOfferTotototo 方法](sql-api-java-samples.md#offer-examples)允許您縮放預配的輸送量值。 

使用 .NET SDK 時[，DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet)方法允許您檢索容器或資料庫的最小輸送量。 

您可以隨時縮放容器或資料庫的預配輸送量。 執行縮放操作以提高輸送量時，由於系統任務而預配所需資源可能需要更長時間。 您可以在 Azure 門戶中檢查縮放操作的狀態，或使用 SDK 以程式設計方式檢查規模操作的狀態。 使用 .Net SDK 時，可以使用 方法`DocumentClient.ReadOfferAsync`獲取縮放操作的狀態。

## <a name="comparison-of-models"></a>模型的比較

|**參數**  |**資料庫上佈建的輸送量**  |**容器上佈建的輸送量**|
|---------|---------|---------|
|RU 數目下限 |400 (在前四個容器之後，每個額外的容器至少需要每秒 100 RU)。 |400|
|每個容器的 RU 數目下限|100|400|
|RU 數目上限|在資料庫上無限制。|在容器上無限制。|
|指派給或適用於特定容器的 RU|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|
|容器的儲存體上限|無限制。|無限制。|
|容器中每個邏輯分割區的輸送量上限|10K RU|10K RU|
|容器中每個邏輯分割區的儲存體上限 (資料 + 索引)|20 GB|20 GB|

## <a name="next-steps"></a>後續步驟

* 瞭解有關[邏輯分區](partition-data.md)的更多詳細資訊。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。

