---
title: 使用 Azure 網站恢復在 Azure VM 容錯移轉後保留 IP 位址
description: 說明如何在容錯移轉 Azure VM 時保留 IP 位址，以便使用 Azure Site Recovery 災害復原至次要區域
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257559"
---
# <a name="retain-ip-addresses-during-failover"></a>在容錯轉移期間保留 IP 位址

[Azure Site Recovery](site-recovery-overview.md) 透過將 VM 複寫至另一個 Azure 區域、發生中斷時容錯移轉，並在恢復正常時容錯回復到主要區域，來啟用 Azure VM 的災害復原。

在容錯移轉期間，您可能希望保留目標區域中的 IP 位址與來源區域相同：

- 根據預設，當您為 Azure VM 啟用災害復原時，Site Recovery 會根據來源資源設定建立目標資源。 針對使用靜態 IP 位址設定的 Azure VM，Site Recovery 會嘗試為目標 VM 佈建相同的 IP 位址 (如果它未在使用中)。 如需 Site Recovery 如何處理定址的完整說明，請[檢閱這篇文章](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)。
- 針對簡單的應用程式，預設設定已足夠。 針對更複雜的應用程式，您可能需要佈建額外的資源，以確保在容錯移轉之後連線能力如預期般工作。


這篇文章會提供一些在更複雜的範例案例中保留 IP 位址的範例。 範例包括：

- 針對所有資源在 Azure 中執行的公司進行容錯移轉
- 針對使用混合式部署，且資源同時於內部部署和 Azure 中執行的公司進行容錯移轉

## <a name="resources-in-azure-full-failover"></a>Azure 中的資源：完整的容錯移轉

公司 A 的所有應用程式都在 Azure 中執行。

### <a name="before-failover"></a>容錯移轉前

這是容錯移轉之前的架構。

- 公司 A 在來源和目標 Azure 區域中具有相同的網路和子網路。
- 為了減少恢復時間目標 （RTO），公司使用 SQL Server 始終打開的副本節點、網域控制站等。這些副本節點位於目的地區域中的不同 VNet 中，因此它們可以在源區域和目的地區域之間建立 VPN 網站到網站的連接。 如果在來源和目標中使用相同的 IP 位址空間，則無法進行此操作。  
- 在容錯移轉之前，網路架構如下所示：
    - 主要區域是 Azure 東亞
        - 東亞有一個 VNet (**來源 VNet**)，位址空間為 10.1.0.0/16。
        - 東亞的工作負載分為 VNet 中的三個子網路：
            - **子網路 1**：10.1.1.0/24
            - **子網 2**： 10.1.2.0/24
            - **子網路 3**：10.1.3.0/24
    - 次要 (目標) 區域是 Azure 東南亞
        - 東南亞有一個復原 VNet (**復原 VNet**) 與**來源 VNet** 相同。
        - 東南亞有一個額外的 VNet (**Azure VNet**)，位址空間為 10.2.0.0/16。
        - **Azure VNet** 包含子網路 (**子網路 4**)，位址空間為 10.2.4.0/24。
        - SQL 伺服器始終打開、網域控制站等的副本節點位於**子網 4**中。
    - **來源 VNet** 和 **Azure VNet** 是透過 VPN 站對站連線來連線。
    - **Recovery VNet** 並未與任何其他虛擬網路連線。
    - **公司 A** 會針對已複寫的項目指派/驗證目標 IP 位址。 每個 VM 的目標 IP 皆等同於來源 IP。

![完整容錯移轉之前 Azure 中的資源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>容錯移轉後

如果發生來源區域性中斷，則公司 A 可以將其所有資源容錯移轉至目標區域。

- 如果在容錯移轉之前已有目標 IP 位址，則公司 A 可以協調容錯移轉，並在**復原 VNet** 和 **Azure VNet** 之間進行容錯移轉之後自動建立連線。 下圖提供相關說明.
- 根據應用程式需求，可在容錯移轉之前、期間 (作為中繼步驟) 或之後於目標區域上建立兩個 VNet 之間 (**復原 VNet** 和 **Azure VNet**) 的連線。
  - 公司可以使用[復原計劃](site-recovery-create-recovery-plans.md)來指定何時建立連線。
  - 他們可以使用 VNet 對等互連或站對站 VPN 在 VNet 之間進行連線。
      - VNet 對等互連不會使用 VPN 閘道，且具有不同的條件約束。
      - VNet 對等互連[價格](https://azure.microsoft.com/pricing/details/virtual-network)與 VNet 對 VNet VPN 閘道[價格](https://azure.microsoft.com/pricing/details/vpn-gateway)的計算方式不同。 針對容錯移轉，我們通常建議使用與來源網路相同的連線能力方法 (包括連線類型) 以最小化無法預期的網路事件。

    ![Azure 中的資源：完整的容錯移轉](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Azure 中的資源：隔離的應用程式容錯移轉

您可能需要在應用程式層級進行容錯移轉。 例如，容錯移轉位於專用子網路中的特定應用程式或應用程式層。

- 在此案例中，儘管您可以保留 IP 位址，但通常不建議這樣做，因為它會增加連線能力不一致的可能性。 您也會遺失針對位於相同 Azure VNet 內其他子網路的子網路連線能力。
- 進行子網路層級應用程式容錯移轉的更好方法是使用不同的目標 IP 位址進行容錯移轉 (如果需要連線到來源 VNet 上的其他子網路)，或者在來源區域中隔離其本身專用 VNet 中的每個應用程式。 使用第二種方法時，您可以在來源區域中的網路之間建立連線能力，並在容錯移轉至目標區域時模擬相同行為。  

在此範例中，公司 A 將來源區域中的應用程式放置在專用 VNet 中，並在這些 VNet 之間建立連線能力。 透過此設計，他們可以執行隔離的應用程式容錯移轉，並在目標網路中保留來源私人 IP 位址。

### <a name="before-failover"></a>容錯移轉前

在容錯移轉之前，架構如下所示：

- 應用程式 VM 裝載於主要 Azure 東亞區域：
    - **App1** VM 位於 VNet **來源 VNet 1**：10.1.0.0/16。
    - **App2** VM 位於 VNet **來源 VNet 2**：10.2.0.0/16。
    - **來源 VNet 1** 有兩個子網路。
    - **來源 VNet 2** 有兩個子網路。
- 次要 (目標) 區域是 Azure 東南亞 - 東南亞有與 **來源 VNet 1** 和**來源 VNet 2** 相同的復原 VNet (**復原 VNet 1** 和**復原 VNet 2**)。
        - **復原 VNet 1** 和**復原 VNet 2**都有兩個與**來源 VNet 1** 和**來源 VNet 2** 中的子網路相符的子網路 - 東南亞有一個額外的 VNet (**Azure VNet**)，位址空間為 10.3.0.0/16。
        - **Azure VNet** 包含一個子網路 (**子網路 4**)，位址空間為 10.3.4.0/24。
        - SQL 伺服器始終打開的複製節點、網域控制站等位於**子網 4**中。
- 有許多站對站 VPN 連線： 
    - **來源 VNet 1** 和 **Azure VNet**
    - **來源 VNet 2** 和 **Azure VNet**
    - **來源 VNet 1** 和**來源 VNet 2** 與 VPN 站對站連線
- **復原 VNet 1** 和**復原 VNet 2** 未連線到任何其他 VNet。
- **公司 A** 在**復原 VNet 1** 和**復原 VNet 2** 上設定 VPN 閘道，以縮短 RTO。  
- **Recovery VNet1** 和 **Recovery VNet2** 並未與任何其他虛擬網路連線。
- 為了降低復原時間目標 (RTO)，會在容錯移轉之前在 **Recovery VNet1** 和 **Recovery VNet2** 上設定 VPN 閘道。

    ![應用程式容錯移轉之前 Azure 中的資源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>容錯移轉後

如果發生影響單一應用程式的中斷情況或問題 (在我們的範例中為 **Source VNet 2)，公司 A 可以復原受影響的應用程式，如下所示：


- 中斷**來源 VNet1** 和**來源 VNet2** 之間的 VPN 連線，以及**來源 VNet2** 和 **Azure VNet** 之間的 VPN 連線。
- 於**來源 VNet1** 和**復原 VNet2** 之間，以及**復原 VNet2** 和 **Azure VNet** 之間建立 VPN 連線。
- 容錯移轉**來源 VNet2** 中的 VM 到**復原 VNet2**。

![Azure 應用程式容錯移轉中的資源](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- 您可以擴充此範例，以包含更多應用程式和網路連線。 從來源容錯移轉至目標時，建議盡可能遵循群聚連線模型。
- VPN 閘道會使用公用 IP 位址與閘道躍點來建立連線。 如果您不想使用公用 IP 位址或想要避免額外的躍點，則可以使用 [Azure VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)，跨[支援的 Azure 區域](../virtual-network/virtual-network-manage-peering.md#cross-region)針對虛擬網路進行對等互連。

## <a name="hybrid-resources-full-failover"></a>混合式資源：完整的容錯移轉

在此案例中，**公司 B** 執行混合式業務，有一部分的應用程式基礎架構是在 Azure 上執行，其餘則是在內部部署中執行。 

### <a name="before-failover"></a>容錯移轉前

以下是網路架構在容錯移轉前的外觀。

- 應用程式 VM 裝載於 Azure 東亞。
- 東亞有一個 VNet (**來源 VNet**)，位址空間為 10.1.0.0/16。
  - 東亞的工作負載分為**來源 VNet** 中的三個子網路：
    - **子網路 1**：10.1.1.0/24
    - **子網 2**： 10.1.2.0/24
    - **子網 3**： 10.1.3.0/24，利用具有位址空間 10.1.0.0/16 的 Azure 虛擬網路。 此虛擬網路稱為**來源 VNet**
      - 次要 (目標) 區域是 Azure 東南亞：
  - 東南亞有一個復原 VNet (**復原 VNet**) 與**來源 VNet** 相同。
- 東亞的 VM 通過 Azure ExpressRoute 或網站到網站 VPN 連接到本地資料中心。
- 為了降低 RTO，公司 B 在容錯移轉之前於 Azure 東南亞的復原 VNet 上佈建閘道。
- 公司 B 會針對已複寫的 VM 指派/驗證目標 IP 位址。 每個 VM 的目標 IP 皆等同於來源 IP 位址。


![容錯移轉前的內部部署對 Azure 連線能力](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>容錯移轉後


如果發生來源區域性中斷，則公司 B 可以將其所有資源容錯移轉至目標區域。

- 在容錯移轉之前已有目標 IP 位址，公司 B 可以協調容錯移轉，並在**復原 VNet** 和 **Azure VNet** 之間進行容錯移轉之後自動建立連線。
- 根據應用程式需求，可在容錯移轉之前、期間 (作為中繼步驟) 或之後於目標區域上建立兩個 VNet 之間 (**復原 VNet** 和 **Azure VNet**) 的連線。 公司可以使用[復原計劃](site-recovery-create-recovery-plans.md)來指定何時建立連線。
- 介於 Azure 東亞與內部部署資料中心之間的原始連線，應該要在建立 Azure 東南亞與內部部署資料中心之間的連線前先中斷連線。
- 內部部署路由會在容錯移轉之後，重新設定為指向目標區域和閘道。

![容錯移轉後的內部部署對 Azure 連線能力](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>混合式資源：隔離的應用程式容錯移轉

公司 B 無法在子網路層級上對隔離的應用程式進行容錯移轉。 這是因為來源和復原 VNet 上的位址空間是一樣的，且原始來源對內部部署的連線是處於作用中狀態。

 - 針對應用程式復原，公司 B 需要將每個應用程式放在其自己專用的 Azure VNet 中。
 - 將每個應用程式放在不同的 VNet 中，公司 B 可以對獨立的應用程式進行容錯移轉，並將來源連線路由傳送至目標區域。

## <a name="next-steps"></a>後續步驟

深入了解[復原方案](site-recovery-create-recovery-plans.md)。
