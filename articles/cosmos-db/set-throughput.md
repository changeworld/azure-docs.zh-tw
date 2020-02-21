---
title: 在 Azure Cosmos 容器和資料庫上佈建輸送量
description: 了解如何設定 Azure Cosmos 容器和資料庫的佈建輸送量。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 39eacbb9a87fa18cc6ef92e319fbfbd3e415337b
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/21/2020
ms.locfileid: "77525510"
---
# <a name="provision-throughput-on-containers-and-databases"></a>在容器和資料庫中佈建輸送量

Azure Cosmos 資料庫是一組容器的管理單位。 資料庫是由一組無從驗證結構描述的容器所組成的。 Azure Cosmos 容器是適用於輸送量和儲存體的延展性單位。 容器會跨 Azure 區域內的一組機器進行水平分割，並散發到與您 Azure Cosmos 帳戶相關聯的所有 Azure 區域。

有了 Azure Cosmos DB，您就可以在兩個數據細微性布建輸送量：
 
- Azure Cosmos 容器
- Azure Cosmos 資料庫

## <a name="set-throughput-on-a-container"></a>在容器上設定輸送量  

在 Azure Cosmos 容器上布建的輸送量是專為該容器保留的。 該容器隨時都可接收佈建輸送量。 容器上的佈建輸送量在財務方面會由 SLA 所支援。 若要瞭解如何在容器上設定輸送量，請參閱[在 Azure Cosmos 容器上](how-to-provision-container-throughput.md)布建輸送量。

在容器上設定布建的輸送量是最常使用的選項。 您可以使用[要求單位（ru）](request-units.md)布建任何數量的輸送量，以彈性調整容器的輸送量。 

為容器布建的輸送量會平均分散于其實體分割區中，而且假設有一個良好的資料分割索引鍵，可在實體磁碟分割之間平均分散邏輯資料分割，則輸送量也會平均分散到所有容器的邏輯分割區。 您無法選擇性地指定邏輯分割區的輸送量。 因為實體分割區會裝載容器的一或多個邏輯分割區；因此，實體分割區專屬於該容器並支援該容器上佈建的輸送量。 

如果邏輯分割區上執行的工作負載所耗用的輸送量超過配置給該邏輯分割區的輸送量，您的作業會有速率限制。 發生速率限制時，您可以提高整個容器的布建輸送量，或重試作業。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

當您想要保證容器的效能時，我們建議您在容器資料粒度上設定輸送量。

下圖示範實體分割區如何裝載容器的一或多個邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>在資料庫上設定輸送量

當您在 Azure Cosmos 資料庫上布建輸送量時，會在資料庫中的所有容器（稱為共用資料庫容器）之間共用輸送量。 如果您在資料庫中的特定容器上指定布建的輸送量，就會發生例外狀況。 在其容器之間共用資料庫層級布建的輸送量，類似于在電腦的叢集上裝載資料庫。 因為資料庫共用內的所有容器都會共用機器上可用的資源，因此，您自然不會取得任何特定容器上的可預測效能。 若要瞭解如何在資料庫上設定布建的輸送量，請參閱[在 Azure Cosmos 資料庫上設定布建的輸送量](how-to-provision-database-throughput.md)。

在 Azure Cosmos 資料庫上設定輸送量，保證您隨時都能收到該資料庫的布建輸送量。 因為資料庫共用內的所有容器都會共用佈建輸送量，因此，Azure Cosmos DB 不會針對該資料庫中的特定容器提供任何可預測的輸送量保證。 特定容器可接收的輸送量部分取決於：

* 容器數目。
* 對於各種容器所選擇的分割區索引鍵。
* 散發於容器中各種不同邏輯分割區的工作負載。 

當您想要在多個容器之間共用輸送量，但不想讓輸送量專屬於任何特定容器時，我們建議您在資料庫上設定輸送量。 

以下是最好在資料庫層級佈建輸送量的範例：

* 在一組容器上共用資料庫的佈建輸送量非常適用於多租用戶應用程式。 每位使用者都可透過不同的 Azure Cosmos 容器來表示。

* 當您將從 VM 叢集或從內部部署實體伺服器裝載的 NoSQL 資料庫 (例如 MongoDB 或 Cassandra) 移轉到 Azure Cosmos DB 時，在一組容器之間共用資料庫的佈建輸送量就很實用。 將 Azure Cosmos 資料庫上設定的佈建輸送量想像為 MongoDB 或 Cassandra 叢集計算容量之佈建輸送量的邏輯對等項目 (但更符合成本效益且更具彈性)。  

在具有布建輸送量的資料庫內建立的所有容器，都必須使用分割區索引[鍵](partition-data.md)來建立。 在任何指定的時間點，配置給資料庫內容器的輸送量會分散在該容器的所有邏輯分割區中。 當您的容器共用資料庫上設定的布建輸送量時，您無法選擇性地將輸送量套用到特定容器或邏輯分割區。 

如果邏輯分割區上的工作負載所取用的輸送量超過配置給特定邏輯分割區的輸送量時，您的作業將會受到速率限制。 發生速率限制時，您可以增加整個資料庫的輸送量，或重試作業。 如需有關分割的詳細資訊，請參閱[邏輯分割區](partition-data.md)。

共用輸送量資料庫中的容器會共用配置給該資料庫的輸送量（RU/秒）。 在資料庫上，最多可以有四個容器，而且至少有 400 RU/秒。 前四個容器之後的每個新容器都需要額外 100 RU/秒的最小值。 例如，如果您有包含八個容器的共用輸送量資料庫，資料庫上的最小 RU/秒將是 800 RU/秒。

> [!NOTE]
> 在共用的輸送量資料庫中，資料庫中最多可以有25個容器。 如果您在共用輸送量資料庫中已有25個以上的容器，在容器計數小於25之前，您將無法建立其他容器。

如果您的工作負載牽涉到刪除和重新建立資料庫中的所有集合，建議您卸載空的資料庫，然後在建立集合之前，先重新建立新的資料庫。 下圖顯示實體分割區如何裝載一或多個屬於資料庫內不同容器的邏輯分割區：

![實體分割區](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>在資料庫和容器上設定輸送量

您可以結合這兩個模型， 就能同時在資料庫和容器上佈建輸送量。 下列範例說明如何在 Azure Cosmos 資料庫和容器上佈建輸送量：

* 您可以建立名為*Z*的 Azure Cosmos 資料庫，並布建輸送量為 *"K"* ru。 
* 接下來，在資料庫中建立五個名為*A*、 *B*、 *C*、 *D*和*E*的容器。 建立容器 B 時，請務必啟用 [**為此容器布建專用輸送量**] 選項，並在此容器上明確設定 *"P"* 個 ru 的布建輸送量。 請注意，只有在建立資料庫和容器時，才可以設定共用和專用輸送量。 

   ![設定容器層級的輸送量](./media/set-throughput/coll-level-throughput.png)

* *"K"* ru 輸送量會在四個容器*A*、 *C*、 *D*和*E*之間共用。可供*A*、 *C*、 *D*或*E*使用的確切輸送量數量會有所不同。 而且沒有適用於每個個別容器輸送量的 SLA。
* 名為*B*的容器保證會隨時取得 *"P"* 個 ru 輸送量。 並受到 SLA 支援。

> [!NOTE]
> 具有布建輸送量的容器無法轉換為共用資料庫容器。 相反地，共用資料庫容器無法轉換成具有專用的輸送量。

## <a name="update-throughput-on-a-database-or-a-container"></a>更新資料庫或容器的輸送量

建立 Azure Cosmos 容器或資料庫之後，您可以更新布建的輸送量。 您可以在資料庫或容器上設定的最大布建輸送量沒有任何限制。 布建的最小輸送量視下列因素而定： 

* 您在容器中儲存的資料大小上限
* 您在容器上布建的最大輸送量
* 您在具有共用輸送量的資料庫中建立的 Azure Cosmos 容器數目上限。 

您可以使用 Sdk 或查看 Azure 入口網站中的值，以程式設計方式抓取容器或資料庫的最小輸送量。 使用 .NET SDK 時， [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet)方法可讓您調整已布建的輸送量值。 使用 JAVA SDK 時， [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples)方法可讓您調整已布建的輸送量值。 

使用 .NET SDK 時， [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet)方法可讓您取得容器或資料庫的最小輸送量。 

您可以隨時調整容器或資料庫的布建輸送量。 執行調整作業以增加輸送量時，可能需要較長的時間，因為系統工作會布建所需的資源。 您可以在 Azure 入口網站中，或以程式設計方式使用 Sdk 來檢查調整作業的狀態。 使用 .Net SDK 時，您可以使用 `DocumentClient.ReadOfferAsync` 方法來取得調整作業的狀態。

## <a name="comparison-of-models"></a>模型的比較

|**參數**  |**資料庫上佈建的輸送量**  |**容器上佈建的輸送量**|
|---------|---------|---------|
|RU 數目下限 |400 (在前四個容器之後，每個額外的容器至少需要每秒 100 RU)。 |400|
|每個容器的 RU 數目下限|100|400|
|RU 數目上限|在資料庫上無限制。|在容器上無限制。|
|指派給或適用於特定容器的 RU|不提供保證。 指派給指定容器的 RU 視屬性而定。 屬性可以選擇是共用輸送量之容器的資料分割索引鍵、工作負載散發，以及容器的數目。 |設定於容器上的所有 RU 都是專為該容器保留的。|
|容器的儲存體上限|無限制。|無限制。|
|容器中每個邏輯分割區的輸送量上限|10K RU|10K RU|
|容器中每個邏輯分割區的儲存體上限 (資料 + 索引)|10 GB|10 GB|

## <a name="next-steps"></a>後續步驟

* 深入了解[邏輯分割區](partition-data.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。

