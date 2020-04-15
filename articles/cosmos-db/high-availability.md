---
title: Azure Cosmos DB 中的高可用性
description: 這篇文章說明 Azure Cosmos DB 如何提供高可用性
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 2afeae937d56a84c39167ad55a57c86f2623e52d
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382715"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Azure Cosmos DB 的高可用性

Azure Cosmos DB 會以透明方式，在與 Cosmos 帳戶相關聯的所有 Azure 區域之間複寫您的資料。 Cosmos DB 針對您的資料採用多層備援，如下圖所示：

![實體資料分割](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos 容器中的資料是[水平分割區的](partitioning-overview.md)。

- 在每個區域內，每個分割區都會受到一個複本集所保護，而且所有寫入都會由大多數複本所複寫且永久認可。 複本會分散到多達 10-20 個容錯網域中。

- 所有區域中的每個分割區都會複寫。 每個區域都包含 Cosmos 容器的所有資料分割區，並且可以接受寫入與處理讀取。  

如果您的 Cosmos 帳戶分佈在*N* Azure 區域,則所有數據將至少有*N* x 4 個副本。 除了提供低延遲數據訪問和跨與Cosmos帳戶關聯的區域縮放寫入/讀取輸送量外,擁有更多區域(更高的*N)* 還進一步提高了可用性。  

## <a name="slas-for-availability"></a>用於提供可用性的 SLA

作為全域散發的資料庫，Cosmos DB 會提供全方位的 SLA，以包含輸送量、第 99 個百分位數的延遲、一致性和高可用性。 下表說明 Cosmos DB 針對單一和多重區域帳戶所提供的高可用性相關保證。 為獲得高可用性,請始終將Cosmos帳戶配置為具有多個寫入區域。

|作業類型  | 單一區域 |多重區域 (單一區域寫入)|多重區域 (多重區域寫入) |
|---------|---------|---------|-------|
|寫入    | 99.99    |99.99   |99.999|
|讀取     | 99.99    |99.999  |99.999|

> [!NOTE]
> 實際上,有限過時、會話、一致首碼和最終一致性模型的實際寫入可用性明顯高於已發佈的 SL。 所有一致性層級的實際讀取可用性，明顯高於已發行的 SLA。

## <a name="high-availability-with-cosmos-db-in-the-event-of-regional-outages"></a>Cosmos DB 在發生區域性中斷時的高可用性

區域性中斷並不罕見，Azure Cosmos DB 可確保您的資料庫永遠具有高度可用性。 以下詳細資訊擷取中斷期間 Cosmos DB 行為,具體取決於您的 Cosmos 帳戶設定:

- 使用 Cosmos DB，將寫入作業認可至用戶端之前，資料會由接受寫入作業之區域內的複本仲裁進行永久認可。

- 使用多重寫入區域設定的多重區域帳戶，將針對寫入和讀取維持高可用性。 區域性容錯移轉是即時的，不需要對應用程式進行任何變更。

- 單一區域帳戶可能會在區域性中斷之後失去可用性。 始終建議使用Cosmos帳戶設置**至少兩個區域**(最好是至少兩個寫入區域),以確保始終高可用性。

### <a name="multi-region-accounts-with-a-single-write-region-write-region-outage"></a>具有單寫入區域的多區域帳戶(寫入區域中斷)

- 在寫入區域中斷期間,在 Azure Cosmos 帳戶上配置**自動故障轉移**時,Cosmos 帳戶將自動將輔助區域提升為新的主寫入區域。 啟用後,故障轉移將按指定的區域優先順序順序發生到另一個區域。
- 當以前受影響的區域重新連線時,任何在區域失敗時未複製的寫入數據都可通過[衝突源](how-to-manage-conflicts.md#read-from-conflict-feed)提供。 應用程式可以讀取衝突源,根據特定於應用程式的邏輯解決衝突,並根據需要將更新的數據寫回 Azure Cosmos 容器。
- 一旦先前受影響的寫入區域復原，它將自動作為讀取區域使用。 您可以切換回恢復區域作為寫入區域。 可以使用[PowerShell、Azure CLI 或 Azure 門戶](how-to-manage-database-account.md#manual-failover)來切換區域。 切換寫入區域之前、期間或之後**不會丟失資料或可用性**,並且應用程式仍然高度可用。

> [!IMPORTANT]
> 強烈建議您設定用於生產工作負載的 Azure Cosmos 帳號 **,以啟用自動故障轉移**。 手動故障轉移需要輔助寫入區域和主寫入區域之間的連接才能完成一致性檢查,以確保故障轉移期間不會丟失數據。 如果主區域不可用,則此一致性檢查無法完成,手動故障轉移將無法成功,從而導致寫入可用性損失。

### <a name="multi-region-accounts-with-a-single-write-region-read-region-outage"></a>具有單寫入區域的多區域帳戶(讀取區域中斷)

- 在讀取區域中斷期間,使用任何一致性級別或與三個或多個讀取區域強一致性的 Cosmos 帳戶將保持高度可用於讀取和寫入。
- 受影響的區域將自動斷開連接,並將標記為離線。 [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)將重定向到首選區域清單中的下一個可用區域。
- 如果慣用區域清單中的區域都無法使用，呼叫會自動切換回目前的寫入區域。
- 不需要對應用程式的程式碼做任何變更來處理讀取區域中斷。 當受影響的讀取區域重新連線時,它將自動與當前寫入區域同步,並再次可用於提供讀取請求。
- 後續的讀取會被重新導向至復原的區域，不需要對應用程式的程式碼做任何變更。 在故障轉移和重新聯接以前失敗的區域期間,Cosmos DB 將繼續遵守讀取一致性保證。

> [!IMPORTANT]
> 使用與兩個或更少讀取區域強一致性的 Azure Cosmos 帳戶將在讀取區域中斷期間失去寫入可用性,但將保持剩餘區域的讀取可用性。

- 即使在 Azure 區域永久不可恢復時罕見且不幸的事件中,如果多區域 Cosmos 帳戶*配置強一*致性,則不會丟失任何數據丟失。 如果永久不可復原的寫入區域(配置了具有有限過期一致性的多區域 Cosmos 帳戶),則潛在的數據丟失視窗僅限於過期視窗 *(K*或*T),* 其中 K= 100,000 更新和 T=5 分鐘。 對於會話、一致首碼和最終一致性級別,潛在資料丟失視窗最多限制為15分鐘。 有關 Azure Cosmos DB 的 RTO 和 RPO 目標的詳細資訊,請參閱[一致性級別和資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="availability-zone-support"></a>可用性區域支援

除了跨區域恢復能力外,現在還可以在選擇要與 Azure Cosmos 資料庫關聯的區域時啟用**區域冗餘**。

在可用性區域支援下,Azure Cosmos DB 將確保副本放置在給定區域內的多個區域之間,以在區域故障期間提供高可用性和彈性。 此配置中沒有對延遲和其他 SL 的更改。 在單個區域發生故障時,區域冗餘提供具有 RPO_0 的完整數據持久性和 RTO=0 的可用性。

區域冗餘是[多主機複製](how-to-multi-master.md)功能*的補充功能*。 單靠區域冗餘無法實現區域彈性。 例如,如果區域中斷或跨區域的延遲訪問較低,建議除了區域冗餘外,還具有多個寫入區域。

為 Azure Cosmos 帳戶配置多區域寫入時,可以選擇加入區域冗餘,無需額外付費。 否則,請參閱以下說明區域冗餘支持的定價。 您可以通過刪除區域並在啟用區域冗餘後將其添加回,在 Azure Cosmos 帳戶的現有區域上啟用區域冗餘。

此功能在以下 Azure 區域中可用:

- 英國南部

- 東南亞

- 美國東部

- 美國東部 2

- 美國中部

- 西歐

- 美國西部 2

> [!NOTE]
> 為單個區域啟用可用區域 Azure Cosmos 帳戶將導致費用,相當於向帳戶添加其他區域。 有關定價的詳細資訊,請參閱 Azure Cosmos DB 文章中的[定價頁](https://azure.microsoft.com/pricing/details/cosmos-db/)和[多區域成本](optimize-cost-regions.md)。

下表總結了各種帳戶配置的高可用性功能:

|KPI  |沒有可用區域的單一區域(非 AZ)  |具有可用區域 (AZ) 的單一區域  |帶可用性區域(AZ,2 個區域)的多區域寫入 - 最推薦的設定 |
|---------|---------|---------|---------|
|寫入可用性 SLA | 99.99% | 99.99% | 99.999% |
|讀取可用性 SLA  | 99.99% | 99.99% | 99.999% |
|Price | 單區域計費率 | 單一區域可用性區域計費費率 | 多區域計費率 |
|區域故障 + 資料遺失 | 資料遺失 | 沒有資料遺失 | 沒有資料遺失 |
|區域容錯與可用性 | 可用性損失 | 無可用性損失 | 無可用性損失 |
|讀取延遲 | 交叉區域 | 交叉區域 | 低 |
|寫入延遲 | 交叉區域 | 交叉區域 | 低 |
|區域中斷 + 資料遺失 | 資料遺失 |  資料遺失 | 資料遺失 <br/><br/> 當對多主控區和多個區域使用有限過期一致性時,資料丟失僅限於帳戶上配置的邊界過時 <br /><br />通過配置與多個區域的強一致性,可以避免區域中斷期間的數據丟失。 此選項附帶影響可用性和性能的權衡。 它只能在配置為單區域寫入的帳戶上配置。 |
|區域中斷和可用性 | 可用性損失 | 可用性損失 | 無可用性損失 |
|Throughput | X RU/s 預配輸送量 | X RU/s 預配輸送量 | 2X RU/s 預配輸送量 <br/><br/> 與具有可用區的單個區域相比,此配置模式需要的輸送量是單個區域的兩倍,因為存在兩個區域。 |

> [!NOTE]
> 要為多區域 Azure Cosmos 帳戶啟用可用性區域支援,該帳戶必須啟用多主機寫入。

可以將區域添加到新的或現有的 Azure Cosmos 帳戶時啟用區域冗餘。 要在 Azure Cosmos 帳戶上啟用區域冗餘`isZoneRedundant`,應`true`為特定 位置設置標誌。 您可以在位置屬性中設置此標誌。 例如,以下 PowerShell 程式碼段支援「東南亞」區域的區域冗餘:

```powershell
$locations = @(
    @{ "locationName"="Southeast Asia"; "failoverPriority"=0; "isZoneRedundant"= "true" },
    @{ "locationName"="East US"; "failoverPriority"=1; "isZoneRedundant"= "true" }
)
```

以下命令展示如何為「EastUS」和「WestUS2」區域啟用區域冗餘:

```azurecli-interactive
az cosmosdb create \
  --name mycosmosdbaccount \
  --resource-group myResourceGroup \
  --kind GlobalDocumentDB \
  --default-consistency-level Session \
  --locations regionName=EastUS failoverPriority=0 isZoneRedundant=True \
  --locations regionName=WestUS2 failoverPriority=1 isZoneRedundant=True
```

在創建 Azure Cosmos 帳戶時,可以使用 Azure 門戶啟用可用性區域。 建立帳號時,請確保啟用**地理冗餘**,**多區域寫入**,並選擇支援可用性區域的區域:

![使用 Azure 門戶啟用可用性區域](./media/high-availability/enable-availability-zones-using-portal.png) 

## <a name="building-highly-available-applications"></a>高度可用的應用程式

- 若要確保寫入和讀取高可用性，請將 Cosmos 帳戶設定為跨越至少兩個具有多重寫入區域的區域。 此設定將為 SL 支援的讀取和寫入提供最高可用性、最低延遲和最佳可擴充性。 若要深入了解，請參閱如何[使用多重寫入區域設定您的 Cosmos 帳戶](tutorial-global-distribution-sql-api.md)。

- 對設定為單寫區域的多區域 Cosmos 帳號,請使用[Azure CLI 或 Azure 門戶啟用自動故障轉移](how-to-manage-database-account.md#automatic-failover)。 啟用自動容錯移轉之後，只要發生區域性災難，Cosmos DB 就會自動容錯移轉您的帳戶。  

- 即使您的 Azure Cosmos 帳戶非常可用,您的應用程式也可能無法正確設計為保持高度可用。 要測試應用程式的端到端高可用性,作為應用程式測試或災難恢復 (DR) 演練的一部分,請暫時禁用帳戶的自動故障轉移,[使用 PowerShell、Azure CLI 或 Azure 門戶調用手動故障轉移](how-to-manage-database-account.md#manual-failover),然後監視應用程式的故障轉移。 完成後,您可以故障返回主區域並還原帳戶的自動故障轉移。

- 在全球分散式資料庫環境中,在區域範圍內發生中斷時,一致性級別和數據持久性之間存在直接關係。 當您開發商務持續性計劃時，您必須了解應用程式在干擾性事件之後完全復原所需的最大可接受時間。 完全復原應用程式所需的時間，也稱為復原時間目標 (RTO)。 您也必須了解在干擾性事件之後復原時，應用程式可忍受遺失的最近資料更新最大期間。 您可能經得起遺失的更新時間週期，也稱為復原點目標 (RPO)。 若要查看 Azure Cosmos DB 的 RPO 與 RTO請參閱[一致性層級與資料持久性](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>後續步驟

接下來，您可以閱讀下列文章：

- [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
- [全域調整佈建的輸送量](scaling-throughput.md)
- [全域散發 - 運作原理](global-dist-under-the-hood.md)
- [Azure 宇宙 DB 中的一致性層級](consistency-levels.md)
- [如何設定有多個寫入區域的 Cosmos 帳戶](how-to-multi-master.md)
