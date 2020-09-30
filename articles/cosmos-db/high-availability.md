---
title: Azure Cosmos DB 中的高可用性
description: 這篇文章說明 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 1f2e90f9391654d10332b9f1a21c56fd22e2307b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570802"
---
# <a name="how-does-azure-cosmos-db-provide-high-availability"></a>Azure Cosmos DB 如何提供高可用性？ 

Azure Cosmos DB 會以透明方式將您的資料複寫到與 Azure Cosmos 帳戶相關聯的所有 Azure 區域。 Azure Cosmos DB 針對您的資料採用多層冗余，如下圖所示：

:::image type="content" source="./media/high-availability/cosmosdb-data-redundancy.png" alt-text="實體資料分割" border="false":::

- Azure Cosmos 容器內的資料會以 [水準方式分割](partitioning-overview.md)。

- 分割集是多個複本集的集合。 在每個區域內，每個分割區都會受到一個複本集所保護，而且所有寫入都會由大多數複本所複寫且永久認可。 複本會分散到多達 10-20 個容錯網域中。

- 所有區域中的每個分割區都會複寫。 每個區域都包含 Azure Cosmos 容器的所有資料分割區，而且可以接受寫入和服務讀取。  

如果您的 Azure Cosmos 帳戶分散在 *N* 個 azure 區域中，則所有資料至少會有 *N* x 4 個複本。 在超過2個區域中，Azure Cosmos 帳戶通常會改善應用程式的可用性，並在相關聯的區域之間提供低延遲。 

## <a name="slas-for-availability"></a>用於提供可用性的 SLA

做為全域散發的資料庫，Azure Cosmos DB 提供涵蓋輸送量、第99個百分位數的延遲、一致性和高可用性的完整 Sla。 下表顯示單一和多重區域帳戶 Azure Cosmos DB 所提供的高可用性保證。 為了達到高可用性，請一律將您的 Azure Cosmos 帳戶設定為具有多個寫入區域。

|作業類型  | 單一區域 |多重區域 (單一區域寫入)|多重區域 (多重區域寫入) |
|---------|---------|---------|-------|
|寫入    | 99.99    |99.99   |99.999|
|讀取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 實際上，限定過期、會話、一致前置詞和最終一致性模型的實際寫入可用性，明顯高於已發行的 Sla。 所有一致性層級的實際讀取可用性，明顯高於已發行的 SLA。

## <a name="high-availability-with-azure-cosmos-db-in-the-event-of-regional-outages"></a>發生區域性中斷時，Azure Cosmos DB 的高可用性

在發生區域性中斷的罕見情況下，Azure Cosmos DB 可確保您的資料庫永遠具有高度可用性。 下列詳細資料會根據您的 Azure Cosmos 帳戶設定，在中斷期間捕獲 Azure Cosmos DB 行為：

- 使用 Azure Cosmos DB，在將寫入作業認可至用戶端之前，資料會由接受寫入作業之區域內的複本仲裁進行永久認可。

- 使用多重寫入區域設定的多重區域帳戶，將針對寫入和讀取維持高可用性。 區域性容錯移轉是即時的，不需要對應用程式進行任何變更。

- 單一區域帳戶可能會在區域性中斷之後失去可用性。 一律建議至少設定 **兩個區域** (最好是至少兩個寫入區域) 使用您的 Azure Cosmos 帳戶，以確保隨時都有高可用性。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>具有單一寫入區域 (寫入區域中斷的多重區域帳戶) 

- 在寫入區域中斷期間，Azure Cosmos 帳戶會在 Azure Cosmos 帳戶上設定 **啟用自動容錯移轉** 時，自動將次要區域升級為新的主要寫入區域。 啟用時，會依照您所指定的區域優先順序順序，將容錯移轉到另一個區域。
- 當先前受影響的區域重新上線時，任何未在區域失敗時複寫的寫入資料，都會透過 [衝突](how-to-manage-conflicts.md#read-from-conflict-feed)摘要提供。 應用程式可以讀取衝突摘要，根據應用程式特定邏輯來解決衝突，並適當地將更新的資料寫回 Azure Cosmos 容器。
- 一旦先前受影響的寫入區域復原，它將自動作為讀取區域使用。 您可以切換回已復原的區域做為寫入區域。 您可以使用 [PowerShell、Azure CLI 或 Azure 入口網站](how-to-manage-database-account.md#manual-failover)來切換區域。 在切換寫入區域之前、或之後，您的應用程式會持續成為高可用性之前，不會 **遺失任何資料或可用性** 。

> [!IMPORTANT]
> 強烈建議您設定用於生產工作負載的 Azure Cosmos 帳戶，以 **啟用自動容錯移轉**。 手動容錯移轉需要次要和主要寫入區域之間的連線才能完成一致性檢查，以確保容錯移轉期間不會遺失任何資料。 如果主要區域無法使用，則此一致性檢查無法完成，且手動容錯移轉將不會成功，因而導致寫入可用性遺失。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>具有單一寫入區域 (讀取區域中斷) 的多重區域帳戶

- 在讀取區域中斷期間，使用任何一致性等級或強式一致性與三個或更多讀取區域的 Azure Cosmos 帳戶，將維持高可用性以進行讀取和寫入。
- 使用具有兩個或更少讀取區域之強式一致性的 Azure Cosmos 帳戶 (包括讀取 & 寫入區域) 在讀取區域中斷期間會遺失讀取寫入可用性。
- 受影響的區域會自動中斷連線，並標示為離線。 [Azure Cosmos DB sdk](sql-api-sdk-dotnet.md)會將讀取呼叫重新導向至慣用區域清單中下一個可用的區域。
- 如果慣用區域清單中沒有任何區域可供使用，呼叫就會自動切換回目前的寫入區域。
- 不需要對應用程式的程式碼做任何變更來處理讀取區域中斷。 當受影響的讀取區域重新上線時，它會自動與目前寫入區域同步，並可再次使用以提供讀取要求。
- 無須對您應用程式的程式碼進行任何變更，後續的讀取就會重新導向到已復原的區域。 在先前失敗的區域進行容錯移轉和重新加入期間，Azure Cosmos DB 可繼續接受 read 一致性保證。
- 即使在 Azure 區域永久無法復原的罕見事件中，如果您的多重區域 Azure Cosmos 帳戶設定了 *強* 式一致性，則不會遺失資料。 如果有永久無法復原的寫入區域，以限定過期一致性設定的多重區域 Azure Cosmos 帳戶，可能的資料遺失時間範圍會限制為過期時段 (*k* 或 *t*) 其中 k = 100000 更新和 t = 5 分鐘。 針對會話、一致前置詞和最終一致性層級，可能的資料遺失時間範圍會限制為最多15分鐘。 如需 Azure Cosmos DB 的 RTO 和 RPO 目標的詳細資訊，請參閱 [一致性層級和資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>可用性區域支援

除了跨區域復原之外，您現在可以在選取要與 Azure Cosmos 資料庫建立關聯的區域時啟用 **區域冗余** 。

有了可用性區域支援，Azure Cosmos DB 將可確保將複本放在指定區域內的多個區域，以在區域性失敗期間提供高可用性和復原功能。 此設定中的延遲和其他 Sla 沒有任何變更。 在單一區域失敗的情況下，區域冗余會提供具有 RPO = 0 且可用性與 RTO = 0 的完整資料持久性。

區域冗余是[多重區域寫入功能中](how-to-multi-master.md)複寫的*補充功能*。 您無法依賴區域冗余來達成區域復原。 例如，在區域中斷或跨區域的低延遲存取的情況下，建議您除了區域冗余之外，還有多個寫入區域。

為您的 Azure Cosmos 帳戶設定多區域寫入時，您可以加入宣告區域冗余，而不需要額外費用。 否則，請參閱以下關于區域冗余支援定價的附注。 您可以藉由移除區域並將其新增回已啟用區域冗余的方式，在 Azure Cosmos 帳戶的現有區域上啟用區域冗余。

這項功能適用于： *英國南部、東南亞、美國東部、美國東部2、美國中部、西歐、美國西部2、日本東部、北歐、法國中部、澳大利亞東部、美國東部 2 EUAP* 區域。

> [!NOTE]
> 啟用單一區域 Azure Cosmos 帳戶的可用性區域將會產生相當於將額外區域新增至您帳戶的費用。 如需定價的詳細資訊，請參閱 Azure Cosmos DB 文章中的 [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/) 和 [多區域成本](optimize-cost-regions.md) 。

下表摘要說明各種帳戶設定的高可用性功能：

|KPI  |沒有可用性區域 (非 AZ) 的單一區域  |具有可用性區域 (AZ) 的單一區域  |使用可用性區域 (AZ、2個區域) 的多重區域寫入–最建議的設定 |
|---------|---------|---------|---------|
|寫入可用性 SLA | 99.99% | 99.99% | 99.999% |
|讀取可用性 SLA  | 99.99% | 99.99% | 99.999% |
|價格 | 單一區域計費費率 | 單一區域可用性區域計費費率 | 多區域計費費率 |
|區域失敗–資料遺失 | 資料遺失 | 沒有資料遺失 | 沒有資料遺失 |
|區域失敗-可用性 | 可用性損失 | 無可用性損失 | 無可用性損失 |
|讀取延遲 | 跨區域 | 跨區域 | 低 |
|寫入延遲 | 跨區域 | 跨區域 | 低 |
|區域中斷-資料遺失 | 資料遺失 |  資料遺失 | 資料遺失 <br/><br/> 搭配多個寫入區域和多個區域使用限定過期一致性時，資料遺失會限制在您帳戶上設定的限定過期 <br /><br />您可以設定多個區域的強式一致性，以避免在區域性中斷期間發生資料遺失。 此選項會產生影響可用性和效能的取捨。 只能在針對單一區域寫入設定的帳戶上設定。 |
|區域中斷-可用性 | 可用性損失 | 可用性損失 | 無可用性損失 |
|Throughput | X RU/秒布建的輸送量 | X RU/秒布建的輸送量 | 2X RU/秒布建的輸送量 <br/><br/> 相較于具有可用性區域的單一區域，此設定模式需要兩倍的輸送量（因為有兩個區域）。 |

> [!NOTE]
> 若要啟用多區域 Azure Cosmos 帳戶的可用性區域支援，帳戶必須啟用多區域寫入寫入。

將區域新增至新的或現有的 Azure Cosmos 帳戶時，您可以啟用區域冗余。 若要在您的 Azure Cosmos 帳戶上啟用區域冗余，您應該將旗標設為 `isZoneRedundant` `true` 特定的位置。 您可以在 [位置] 屬性內設定這個旗標。 例如，下列 PowerShell 程式碼片段會啟用「東南亞」區域的區域冗余：

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

下列命令會顯示如何為 "EastUS" 和 "WestUS2" 區域啟用區域冗余：

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

建立 Azure Cosmos 帳戶時，您可以使用 Azure 入口網站來啟用可用性區域。 當您建立帳戶時，請務必啟用地理位置 **冗余**、 **多重區域寫入**，然後選擇支援可用性區域的區域：

:::image type="content" source="./media/high-availability/enable-availability-zones-using-portal.png" alt-text="實體資料分割"::: 

## <a name="building-highly-available-applications"></a>高度可用的應用程式

- 在這些事件期間，檢查 [Azure Cosmos sdk](troubleshoot-sdk-availability.md) 的預期行為，以及影響它的設定。

- 為了確保高寫入和讀取可用性，請將您的 Azure Cosmos 帳戶設定為跨越至少兩個具有多個寫入區域的區域。 這項設定可針對 Sla 所支援的讀取和寫入，提供最高的可用性、最低延遲和最佳的擴充性。 若要深入瞭解，請參閱如何 [使用多個寫入區域來設定您的 Azure Cosmos 帳戶](tutorial-global-distribution-sql-api.md)。

- 針對使用單一寫入區域設定的多重區域 Azure Cosmos 帳戶，請 [使用 Azure CLI 或 Azure 入口網站來啟用自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 啟用自動容錯移轉之後，只要發生區域性災難，Cosmos DB 就會自動容錯移轉您的帳戶。  

- 即使您的 Azure Cosmos 帳戶具有高可用性，但您的應用程式可能無法正確設計以維持高可用性。 若要測試應用程式的端對端高可用性，作為應用程式測試或嚴重損壞修復的一部分 (DR) 演練、暫時停用帳戶的自動容錯移轉、 [使用 PowerShell、Azure CLI 或 Azure 入口網站叫用手動故障](how-to-manage-database-account.md#manual-failover)轉移，然後監視您應用程式的容錯移轉。 完成之後，您可以容錯回復到主要區域，並還原帳戶的自動容錯移轉。

- 在全域散發的資料庫環境中，一致性層級與資料持久性之間存在全區域中斷的關聯性。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 完全復原應用程式所需的時間，也稱為復原時間目標 (RTO)。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能經得起遺失的更新時間週期，也稱為復原點目標 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 與 RTO請參閱[一致性層級與資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>後續步驟

接下來，您可以閱讀下列文章：

- [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
- [全域調整佈建的輸送量](scaling-throughput.md)
- [全域散發 - 運作原理](global-dist-under-the-hood.md)
- [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
- [如何使用多個寫入區域設定您的 Cosmos 帳戶](how-to-multi-master.md)
- [Dns 多區域性環境上的 SDK 行為](troubleshoot-sdk-availability.md)