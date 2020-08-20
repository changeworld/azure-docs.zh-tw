---
title: 整合 Azure ExpressRoute Azure VM 嚴重損壞修復與 Azure Site Recovery
description: 說明如何使用 Azure Site Recovery 和 Azure ExpressRoute 設定 Azure VM 的災害復原
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 0e1f670f2ba5ad31f29d56b2de40acd6e2bf18a9
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654373"
---
# <a name="integrate-expressroute-with-disaster-recovery-for-azure-vms"></a>整合 ExpressRoute 與 Azure Vm 的嚴重損壞修復


本文說明當您將 Azure VM 的災害復原設定為次要 Azure 區域時，如何整合 Azure ExpressRoute 與 [Azure Site Recovery](site-recovery-overview.md)。

Site Recovery 可藉由將 Azure VM 資料複寫至 Azure 來啟用 Azure VM 的災害復原。

- 如果 Azure VM 使用 [Azure 受控磁碟](../virtual-machines/managed-disks-overview.md)，則 VM 資料會複寫到次要區域中已複寫的受控磁碟。
- 如果 Azure VM 未使用受控磁碟，則 VM 資料會複寫至 Azure 儲存體帳戶。
- 複寫端點是公用的，但 Azure VM 的複寫流量不會通過網際網路。

ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft Azure 雲端。 如果您已設定 ExpressRoute，它會以下列方式整合 Site Recovery：

- **在 Azure 區域之間複寫期間**：Azure VM 災害復原的複寫流量會侷限在 Azure 內，因此不需要 ExpressRoute 或將其用於複寫。 不過，如果您要從內部部署網站連線至主要 Azure 網站中的 Azure VM，則在設定這些 Azure VM 的災害復原時，您必須留意某些問題。
- **在 Azure 區域之間容錯移轉**：發生中斷狀況時，您會將 Azure VM 從主要 Azure 區域容錯移轉至次要區域。 在容錯移轉至次要區域之後，必須執行若干步驟，才能使用 ExpressRoute 存取次要區域中的 Azure VM。


## <a name="before-you-begin"></a>開始之前

開始之前，請確定您了解下列概念︰

- ExpressRoute [線路](../expressroute/expressroute-circuit-peerings.md)
- ExpressRoute [路由網域](../expressroute/expressroute-circuit-peerings.md#routingdomains)
- ExpressRoute [位置](../expressroute/expressroute-locations.md)。
- Azure VM [複寫架構](azure-to-azure-architecture.md)
- 如何為 Azure VM [設定複寫](azure-to-azure-tutorial-enable-replication.md)。
- 如何執行 Azure VM 的[容錯移轉](azure-to-azure-tutorial-failover-failback.md)。


## <a name="general-recommendations"></a>一般建議

為遵循最佳做法，並確保災害復原的復原時間目標 (RTO) 符合效率，建議您在設定與 ExpressRoute 整合的 Site Recovery 時執行下列動作：

- 在容錯移轉到次要區域前先佈建網路元件：
    - 當您啟用 Azure VM 的複寫時，Site Recovery 可根據來源網路設定自動部署目標 Azure 區域中的網路資源，例如網路、子網路和閘道。
    - Site Recovery 無法自動設定 VNet 閘道之類的網路資源。
    - 建議您先佈建這些額外的網路資源，再進行容錯移轉。 此部署可能會產生短暫的停機時間，且如果在規劃期間未考量到此停機時間，則其可能會影響整體復原時間。
- 執行定期災害復原演練：
    - 此演練會驗證不遺失資料或不停機的複寫策略，並且不會影響您的生產環境。 此演練有助於避免在最後一刻發生設定問題，而對 RTO 造成負面影響。
    - 當您執行測試容錯移轉以進行演練時，建議您使用個別的 Azure VM 網路，而不是在您啟用複寫時所設定的預設網路。
- 如果您有單一 ExpressRoute 線路，請使用不同的 IP 位址空間。
    - 建議您對目標虛擬網路使用不同的 IP 位址空間。 這樣可避免在區域中斷期間建立連線時發生問題。
    - 如果您無法使用個別的位址空間，請務必在具有不同 IP 位址的個別測試網路上執行災害復原演練測試容錯移轉。 您無法將兩個具有重疊 IP 位址空間的 VNet 連線至相同的 ExpressRoute 線路。

## <a name="replicate-azure-vms-when-using-expressroute"></a>使用 ExpressRoute 複寫 Azure VM


如果您想要在主要網站中設定 Azure Vm 的複寫，並且透過 ExpressRoute 從內部部署網站連線至這些 VM，您將需要執行下列動作：

1. 為每個 Azure VM [啟用複寫](azure-to-azure-tutorial-enable-replication.md)。
2. 選擇性地讓 Site Recovery 設定網路功能：
    - 當您設定並啟用複寫時，Site Recovery 會設定目標 Azure 區域中的網路、子網路和閘道子網路，使其與來源區域中的相符。 Site Recovery 也會進行來源和目標虛擬網路之間的對應。
    - 如果您不想讓 Site Recovery 自動執行此動作，請先建立目標端網路資源，再啟用複寫。
3. 建立其他網路元素：
    - Site Recovery 不會在次要區域中建立路由表、VNet 閘道、VNet 閘道連線、VNet 對等互連，或其他網路資源和連線。
    - 從主要區域執行容錯移轉之前，您必須在次要區域中建立這些額外的網路元素。
    - 您可以使用[復原計畫](site-recovery-create-recovery-plans.md)和自動化指令碼來設定及連接這些網路資源。
1. 如果您已部署網路虛擬設備 (NVA) 以控制網路流量的流程，請注意：
    - Azure 的 Azure VM 複寫預設系統路由是 0.0.0.0/0。
    - 通常，NVA 部署也會定義預設路由 (0.0.0.0/0)，強制連出網際網路流量流經 NVA。 在找不到其他特定的路由設定時，會使用預設路由。
    - 在此情況下，如果所有的複寫流量均通過 NVA，NVA 可能會超載。
    - 使用預設路由將所有 Azure VM 流量路由至內部部署時，也會有此限制。
    - 在此情況下，建議您在虛擬網路中為 Microsoft.Storage 服務[建立一個網路服務端點](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，這樣複寫流量就不會超出 Azure 的範圍。

## <a name="replication-example"></a>複寫範例

企業部署通常會將工作負載分散到多個 Azure VNet，且會以中央連線中樞建立對網際網路和內部部署網站的外部連線。 中樞和輪輻拓撲通常會與 ExpressRoute 搭配使用。

![容錯移轉之前使用 ExpressRoute 從內部部署連線至 Azure](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **區域**。 應用程式部署於 Azure 東亞區域。
- **輪輻 vNet**。 應用程式部署於兩個輪輻 vNet 中：
    - **來源 vNet1**：10.1.0.0/24。
    - **來源 vNet2**：10.2.0.0/24。
    - 每個輪輻虛擬網路分別連線至**中樞 vNet**。
- **中樞 vNet**。 有一個中樞 vNet **來源中樞 vNet**：10.10.10.0/24。
  - 此中樞 vNet 作為閘道管理員。
  - 所有跨子網路的通訊都會通過此中樞。
    - **中樞 vNet 子網**。 此中樞 vNet 有兩個子網路：
    - **NVA 子網路**：10.10.10.0/25。 此子網路包含 NVA (10.10.10.10)。
    - **閘道子網路**：10.10.10.128/25。 此子網路包含已連線到 ExpressRoute 連線的 ExpressRoute 閘道，會透過私人對等互連路由網域路由至內部部署網站。
- 內部部署資料中心有經由香港 Partner Edge 的 ExpressRoute 線路連線。
- 所有路由均可透過 Azure 路由表 (UDR) 來控制。
- 在 vNet 之間輸出或輸出至內部部署資料中心的所有流量，都會透過 NVA 進行路由。

### <a name="hub-and-spoke-peering-settings"></a>中樞與輪輻對等互連設定

#### <a name="spoke-to-hub"></a>輪輻至中樞

**方向** | **設定** | **State**
--- | --- | ---
輪輻至中樞 | 允許虛擬網路位址 | 已啟用
輪輻至中樞 | 允許轉送的流量 | 已啟用
輪輻至中樞 | 允許閘道傳輸 | 已停用
輪輻至中樞 | 使用移除閘道 | 已啟用

 ![輪輻到中樞對等互連設定](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

#### <a name="hub-to-spoke"></a>中樞至輪輻

**方向** | **設定** | **State**
--- | --- | ---
中樞至輪輻 | 允許虛擬網路位址 | 已啟用
中樞至輪輻 | 允許轉送的流量 | 已啟用
中樞至輪輻 | 允許閘道傳輸 | 已啟用
中樞至輪輻 | 使用移除閘道 | 已停用

 ![中樞到輪輻對等互連設定](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="example-steps"></a>範例步驟

在我們的範例中，在來源網路中啟用 Azure VM 的複寫時，應會出現下列情況：

1. 您為 VM [啟用複寫](azure-to-azure-tutorial-enable-replication.md)。
2. Site Recovery 在目標區域中建立複本 vNet、子網路和閘道子網路。
3. Site Recovery 在來源網路與它所建立的複本目標網路之間建立對應。
4. 您以手動方式建立虛擬網路閘道、虛擬網路閘道連線、虛擬網路對等互連，或任何其他網路資源或連線。


## <a name="fail-over-azure-vms-when-using-expressroute"></a>使用 ExpressRoute 進行 Azure VM 的容錯移轉

在您使用Site Recovery 將 Azure VM 容錯移轉至目標 Azure 區域之後，您可以使用 ExpressRoute [私人對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)加以存取。

- 您必須使用新連線將 ExpressRoute 連線至目標 vNet。 現有的 ExpressRoute 連線不會自動轉移。
- 您應如何設定目標 vNet 的 ExpressRoute 連線，取決於您的 ExpressRoute 拓樸。


### <a name="access-with-two-circuits"></a>使用兩個線路存取

#### <a name="two-circuits-with-two-peering-locations"></a>具有兩個對等互連位置的兩個線路

此設定有助於防止 ExpressRoute 線路受到區域性災害的影響。 當您的主要對等互連位置失效時，將可從其他位置繼續連線。

- 連線至生產環境的線路通常是主要線路。 次要線路通常會有較低的頻寬，但可在發生災害時增加。
- 執行容錯移轉後，您可以建立從次要 ExpressRoute 線路到目標 vNet 的連線。 或者，您可以設定並備妥在發生災害時使用的連線，以縮短整體復原時間。
- 同時連線到主要和目標 vNet 時，請確定您的內部部署路由只有在容錯移轉之後才會使用次要線路和連線。
- 在容錯移轉之後，來源和目標 vNet 可接收新的 IP 位址，或保留相同的位址。 在這兩種情況下，可以在容錯移轉之前建立次要連線。


#### <a name="two-circuits-with-single-peering-location"></a>具有單一對等互連位置的兩個線路

這項設定有助於防止主要 ExpressRoute 線路失敗，但若單一 ExpressRoute 對等互連位置失效，而同時對兩個線路造成影響，則無此效用。

- 您可以使用主要線路同時從內部部署資料中心連線至來源 vNet，並使用次要線路連線至目標 vNet。
- 連線至主要和目標 vNet 時，請確定您的內部部署路由只有在容錯移轉之後才會使用次要線路和連線。
-   若這兩個線路是在相同的對等互連位置建立，則無法將這兩個線路連線至相同的 vNet。

### <a name="access-with-a-single-circuit"></a>使用單一線路存取

此設定中只有一個 Expressroute 線路。 雖然線路有備援連線可供停止運作時使用，但若您的對等互連區域停止運作，單一路由線路將無法提供恢復能力。 請注意：

- 您可以將 Azure VM 複寫至[相同地理位置](azure-to-azure-support-matrix.md#region-support)中的任何 Azure 區域。 如果目標 Azure 區域與來源位於不同的位置中，且您使用單一 ExpressRoute 線路，則必須啟用 ExpressRoute Premium。 了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md)和 [ExpressRoute 價格](https://azure.microsoft.com/pricing/details/expressroute/)。
- 如果在目標區域上使用相同的 IP 位址空間，則您無法同時將來源和目標 vNet 連線至線路。 在此情節中：    
    -  中斷來源端的連線，然後建立目標端連線。 您可以將此連線變更編寫為指令碼，作為 Site Recovery 復原計畫的一部分。 請注意：
        - 發生區域性失敗時，如果無法存取主要區域，則中斷連線作業可能會失敗。 這可能會影響建立目標區域連線的進度。
        - 如果您在目標區域中建立連線，且主要區域稍後已復原，則在兩個並行連線嘗試連線到相同的位址空間時，可能會發生封包捨棄的情況。
        - 若要防止此情況，請立即終止主要連線。
        - VM 容錯回復到主要區域之後，將次要連線中斷連線後便可再次建立主要連線。
-   如果在目標 vNet 上使用不同的位址空間，則您可以從相同的 ExpressRoute 線路同時連線至來源和目標 vNet。


## <a name="failover-example"></a>容錯移轉範例

在此處的範例中，我們將使用下列拓撲：

- 位於兩個不同對等互連位置的兩個不同 ExpressRoute 線路。
- 在容錯移轉之後保留 Azure VM 的私人 IP 位址。
- 目標復原區域是 Azure 東南亞。
- 次要 ExpressRoute 線路連線會透過位於新加坡的 Partner Edge 來建立。

對於使用單一 ExpressRoute 線路、在容錯移轉後具有相同 IP 位址的簡易拓樸，請[檢閱這篇文章](site-recovery-retain-ip-azure-vm-failover.md#hybrid-resources-full-failover)。

### <a name="example-steps"></a>範例步驟
若要將此範例中的復原自動化，您必須執行下列作業：

1. 執行設定複寫的步驟。
2. 執行容錯移轉期間或之後的其他步驟，[進行 Azure VM 的容錯移轉](azure-to-azure-tutorial-failover-failback.md)。

    a. 在目標區域中樞 VNet 中建立 Azure ExpressRoute 閘道。 必須有此閘道，才能將目標中樞 vNet 連線至 ExpressRoute 線路。

    b. 建立從目標中樞 vNet 到目標 ExpressRoute 線路的連線。

    c. 在目標區域的中樞和輪輻虛擬網路之間設定 VNet 對等互連。 目標區域上的對等互連屬性將會與來源區域上的相同。

    d. 在中樞 VNet 中設定 UDR 和兩個輪輻 VNet。

    - 使用相同的 IP 位址時，目標端 UDR 的屬性會與來源端上的一樣。
    - 若要使用不同的目標 IP 位址，應該據以修改 UDR。


您可以將上述步驟編寫為指令碼以作為[復原方案](site-recovery-create-recovery-plans.md)的一部分。 視應用程式連線能力和復原時間需求而定，上述步驟也可以在開始容錯移轉之前完成。

#### <a name="after-recovery"></a>復原之後

復原 VM 並完成連線後，復原環境將如下所示。

![容錯移轉之後使用 ExpressRoute 從內部部署連線至 Azure](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## <a name="next-steps"></a>後續步驟

深入了解如何使用[復原計畫](site-recovery-create-recovery-plans.md)自動執行應用程式容錯移轉。
