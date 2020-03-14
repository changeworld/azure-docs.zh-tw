---
title: Azure Cosmos DB 中的高可用性
description: 這篇文章說明 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 0f024bac535ed792d8480c991e470cf5d85932b8
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247419"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB 的高可用性

Azure Cosmos DB 會以透明方式，在與 Cosmos 帳戶相關聯的所有 Azure 區域之間複寫您的資料。 Cosmos DB 針對您的資料採用多層備援，如下圖所示：

![實體資料分割](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器內的資料會以[水準方式分割](partitioning-overview.md)。

- 在每個區域內，每個分割區都會受到一個複本集所保護，而且所有寫入都會由大多數複本所複寫且永久認可。 複本會分散到多達 10-20 個容錯網域中。

- 所有區域中的每個分割區都會複寫。 每個區域都包含 Cosmos 容器的所有資料分割區，並且可以接受寫入與處理讀取。  

如果您的 Cosmos 帳戶分佈在*N*個 Azure 區域中，則所有資料至少會有*N* x 4 個複本。 除了在與您的 Cosmos 帳戶相關聯的區域之間提供低延遲的資料存取和調整寫入/讀取輸送量之外，還有更多區域（更高*N*）可進一步改善可用性。  

## <a name="slas-for-availability"></a>用於提供可用性的 SLA

作為全域散發的資料庫，Cosmos DB 會提供全方位的 SLA，以包含輸送量、第 99 個百分位數的延遲、一致性和高可用性。 下表說明 Cosmos DB 針對單一和多重區域帳戶所提供的高可用性相關保證。 若要獲得高可用性，請一律將您的 Cosmos 帳戶設定為具有多個寫入區域。

|作業類型  | 單一區域 |多重區域 (單一區域寫入)|多重區域 (多重區域寫入) |
|---------|---------|---------|-------|
|寫入    | 99.99    |99.99   |99.999|
|讀取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 實際上，限定過期、會話、一致前置詞和最終一致性模型的實際寫入可用性，明顯高於已發行的 Sla。 所有一致性層級的實際讀取可用性，明顯高於已發行的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>發生區域中斷時，Cosmos DB 的高可用性

區域性中斷並不罕見，Azure Cosmos DB 可確保您的資料庫永遠具有高度可用性。 下列詳細資料會根據您的 Cosmos 帳戶設定，在中斷期間捕捉 Cosmos DB 的行為：

- 使用 Cosmos DB，將寫入作業認可至用戶端之前，資料會由接受寫入作業之區域內的複本仲裁進行永久認可。

- 使用多重寫入區域設定的多重區域帳戶，將針對寫入和讀取維持高可用性。 區域性容錯移轉是即時的，不需要對應用程式進行任何變更。

- 單一區域帳戶可能會在區域性中斷之後失去可用性。 一律建議使用您的 Cosmos 帳戶設定**至少兩個區域**（最好是至少兩個寫入區域），以確保所有時候都能有高可用性。

- **具有單一寫入區域的多重區域帳戶（寫入區域中斷）：**
  - 在寫入區域中斷期間，當 Azure Cosmos 帳戶上設定了 [**啟用自動容錯移轉**] 時，Cosmos 帳戶會自動將次要區域升級為新的主要寫入區域。 啟用時，容錯移轉將會依照您指定的區域優先順序順序，在另一個區域進行。
  - 客戶也可以選擇使用**手動容錯移轉**，並使用自行建立的代理程式來監視其 Cosmos 寫入端點 URL 本身。 對於具有複雜且精密的健全狀況監視需求的客戶，此功能可在寫入區域發生失敗時提供降低的 RTO。
  - 當先前受影響的區域重新上線時，在區域失敗時未複製的任何寫入資料，都可透過[衝突](how-to-manage-conflicts.md#read-from-conflict-feed)摘要取得。 應用程式可以讀取衝突摘要、根據應用程式特定邏輯解決衝突，並適當地將更新的資料寫回 Azure Cosmos 容器。
  - 一旦先前受影響的寫入區域復原，它將自動作為讀取區域使用。 您可以切換回已復原的區域做為寫入區域。 您可以使用[Azure CLI 或 Azure 入口網站](how-to-manage-database-account.md#manual-failover)來切換區域。 在您切換寫入區域的期間或之後，以及您的應用程式持續成為高可用性之前，不會**遺失任何資料或可用性**。

- **具有單一寫入區域的多重區域帳戶（讀取區域中斷）：**
  - 在讀取區域中斷期間，這些帳戶將針對讀取和寫入維持高可用性。
  - 受影響的區域會自動中斷連線，並標示為離線。 [Azure Cosmos DB sdk](sql-api-sdk-dotnet.md)會將讀取呼叫重新導向至慣用區域清單中下一個可用的區域。
  - 如果慣用區域清單中的區域都無法使用，呼叫會自動切換回目前的寫入區域。
  - 不需要對應用程式的程式碼做任何變更來處理讀取區域中斷。 最後，當受影響的區域再度上線時，先前受影響的讀取區域將自動與目前寫入區域同步處理，並可再次用於處理讀取要求。
  - 後續的讀取會被重新導向至復原的區域，不需要對應用程式的程式碼做任何變更。 在先前失敗的區域的容錯移轉和重新加入期間，Cosmos DB 會繼續接受讀取一致性保證。

- 即使在罕見且可惜的事件中，當 Azure 區域永久無法復原時，如果您的多重區域 Cosmos 帳戶已設定*強*式一致性，則不會遺失資料。 在永久無法復原的寫入區域中，使用限定過期一致性設定的多重區域 Cosmos 帳戶，可能的資料遺失時間範圍僅限於過期時段（*k*或*t*），其中 K = 100000 更新和 T = 5 分鐘。 針對會話、一致前置詞和最終一致性層級，可能的資料遺失時間長度上限為15分鐘。 如需 Azure Cosmos DB 的 RTO 和 RPO 目標的詳細資訊，請參閱[一致性層級和資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>可用性區域支援

除了跨區域復原功能，您現在可以在選取要與 Azure Cosmos 資料庫建立關聯的區域時，啟用**區域冗余**。

透過可用性區域支援，Azure Cosmos DB 將確保複本會放在指定區域內的多個區域，以在區域性失敗期間提供高可用性和復原能力。 此設定不會變更延遲和其他 Sla。 在單一區域失敗的情況下，區域冗余會提供具有 RPO = 0 的完整資料持久性，以及具有 RTO = 0 的可用性。

區域冗余是[多重主要](how-to-multi-master.md)複寫功能的*補充功能*。 無法依賴區域冗余來達到區域復原。 例如，在區域中斷或跨區域的低延遲存取的情況下，建議除了區域冗余以外，還有多個寫入區域。

為您的 Azure Cosmos 帳戶設定多區域寫入時，您可以加入宣告區域冗余，而不需要額外付費。 否則，請參閱下面的注意事項，瞭解區域冗余支援的定價。 您可以在 Azure Cosmos 帳戶的現有區域上啟用區域冗余，方法是移除區域，並在啟用區域冗余後將其重新加入。

這項功能適用于下列 Azure 區域：

- 英國南部

- 東南亞

- 美國東部

- 美國東部 2

- 美國中部

- 西歐

- 美國西部 2

> [!NOTE]
> 針對單一區域 Azure Cosmos 帳戶啟用可用性區域，會產生相當於將額外區域新增至您帳戶的費用。 如需定價的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)和[Azure Cosmos DB 文章中的多區域成本](optimize-cost-regions.md)。

下表摘要說明各種帳戶設定的高可用性功能：

|KPI  |不可用性區域的單一區域（非 AZ）  |具有可用性區域的單一區域（AZ）  |使用可用性區域（AZ，2個區域）進行多重區域寫入–最建議的設定 |
|---------|---------|---------|---------|
|寫入可用性 SLA | 99.99% | 99.99% | 99.999% |
|閱讀可用性 SLA  | 99.99% | 99.99% | 99.999% |
|Price | 單一區域計費費率 | 單一區域可用性區域計費費率 | 多區域計費費率 |
|區域失敗–資料遺失 | 資料遺失 | 沒有資料遺失 | 沒有資料遺失 |
|區域失敗–可用性 | 可用性損失 | 無可用性損失 | 無可用性損失 |
|讀取延遲 | 跨區域 | 跨區域 | 低 |
|寫入延遲 | 跨區域 | 跨區域 | 低 |
|區域中斷-資料遺失 | 資料遺失 |  資料遺失 | 資料遺失 <br/><br/> 搭配多個主要和多個區域使用限定過期一致性時，資料遺失僅限於您帳戶上設定的限定過期 <br /><br />藉由設定多個區域的強式一致性，您可以避免在區域性中斷期間遺失資料。 此選項包含會影響可用性和效能的取捨。 只能在設定單一區域寫入的帳戶上進行設定。 |
|區域中斷-可用性 | 可用性損失 | 可用性損失 | 無可用性損失 |
|Throughput | X RU/秒布建輸送量 | X RU/秒布建輸送量 | 2倍 RU/秒布建輸送量 <br/><br/> 相較于具有可用性區域的單一區域，此設定模式需要兩倍的輸送量，因為有兩個區域。 |

> [!NOTE]
> 若要啟用多重區域 Azure Cosmos 帳戶的可用性區域支援，帳戶必須啟用多宿主寫入。

將區域新增至新的或現有的 Azure Cosmos 帳戶時，您可以啟用區域冗余。 若要在您的 Azure Cosmos 帳戶上啟用區域冗余，您應該將特定位置的 [`isZoneRedundant`] 旗標設定為 [`true`]。 您可以在 [位置] 屬性中設定此旗標。 例如，下列 powershell 程式碼片段會啟用「東南亞」區域的區域冗余：

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1 }
)
```

下列命令會顯示如何啟用 "EastUS" 和 "WestUS2" 區域的區域冗余：

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

建立 Azure Cosmos 帳戶時，您可以使用 Azure 入口網站來啟用可用性區域。 當您建立帳戶時，請務必啟用 [**異地冗余**]、[**多重區域寫入**]，然後選擇支援可用性區域的區域：

![使用 Azure 入口網站啟用可用性區域](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>高度可用的應用程式

- 若要確保寫入和讀取高可用性，請將 Cosmos 帳戶設定為跨越至少兩個具有多重寫入區域的區域。 此設定會針對 Sla 所支援的讀取和寫入，提供最高的可用性、最低延遲和最佳擴充性。 若要深入了解，請參閱如何[使用多重寫入區域設定您的 Cosmos 帳戶](tutorial-global-distribution-sql-api.md)。

- 針對使用單一寫入區域設定的多重區域 Cosmos 帳戶，[使用 Azure CLI 或 Azure 入口網站來啟用自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 啟用自動容錯移轉之後，只要發生區域性災難，Cosmos DB 就會自動容錯移轉您的帳戶。  

- 即使您的 Cosmos 帳戶具有高可用性，您的應用程式也可能無法正確設計以維持高可用性。 若要測試應用程式的端對端高可用性，作為應用程式測試或嚴重損壞修復（DR）演練的一部分，請暫時停用帳戶的自動容錯移轉、[使用 Azure CLI 或 Azure 入口網站叫用手動容錯移轉](how-to-manage-database-account.md#manual-failover)，然後監視應用程式的容錯移轉。 完成後，您可以容錯回復到主要區域，並還原帳戶的自動容錯移轉。

- 在全域散發的資料庫環境中，一致性層級與資料持久性在全區域中斷的情況下會有直接關聯性。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 完全復原應用程式所需的時間，也稱為復原時間目標 (RTO)。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能經得起遺失的更新時間週期，也稱為復原點目標 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 與 RTO請參閱[一致性層級與資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>後續步驟

接下來，您可以閱讀下列文章：

- [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
- [全域調整佈建的輸送量](scaling-throughput.md)
- [全域散發 - 運作原理](global-dist-under-the-hood.md)
- [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
- [如何使用多個寫入區域設定您的 Cosmos 帳戶](how-to-multi-master.md)
