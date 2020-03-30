---
title: 關於將快速路由與 Azure 網站恢復結合使用
description: 說明如何搭配使用 Azure ExpressRoute 和 Azure Site Recovery 服務來進行災害復原和移轉。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: e4525bdc6165e8e736db5f539c764d25250cb248
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258001"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute 搭配 Azure Site Recovery

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 透過 ExpressRoute，您可以建立 Microsoft 雲端服務的連線，例如 Microsoft Azure、Office 365 和 Dynamics 365。

本文說明如何使用 Azure ExpressRoute 來搭配 Azure Site Recovery 以進行災害復原和移轉。

## <a name="expressroute-circuits"></a>ExpressRoute 線路

ExpressRoute 線路代表您的內部部署基礎結構與 Microsoft 雲端服務之間，透過連線提供者的邏輯連線。 您可以訂購多條 ExpressRoute 線路。 每一條線路可以位於相同或不同區域，且可透過不同的連線提供者連線到您的內部環境。 請在[這裡](../expressroute/expressroute-circuit-peerings.md)深入了解 ExpressRoute 線路。

ExpressRoute 電路具有與其關聯的多個路由域。 請在[這裡](../expressroute/expressroute-circuit-peerings.md#peeringcompare)深入了解和比較 ExpressRoute 路由網域。

## <a name="on-premises-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 的內部部署到 Azure 複寫

Azure Site Recovery 能夠針對內部部署的 [Hyper-V 虛擬機器](hyper-v-azure-architecture.md)、[VMware 虛擬機器](vmware-azure-architecture.md)和[實體伺服器](physical-azure-architecture.md)進行災害復原並移轉到 Azure。 針對所有內部部署到 Azure 的案例，會將複寫資料傳送到並儲存於 Azure 儲存體帳戶。 在複寫期間，您不需支付任何虛擬機器費用。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器。

網站恢復將資料複製到目標 Azure 區域上的目標 Azure 區域上的 Azure 存儲帳戶或副本託管磁片。這些磁片位於公共終結點上。 要使用 ExpressRoute 進行網站恢復複製流量，可以使用[Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering)或現有[公共對等互連](../expressroute/about-public-peering.md)（用於新創建而棄用）。 Microsoft 對等互連是建議用於複寫的路由網域。 請注意，通過專用對等互連不支援複製。

確保佈建服務器[的網路要求](vmware-azure-configuration-server-requirements.md#network-requirements)也得到滿足。 佈建服務器需要連接到特定 URL 才能進行網站恢復複製的編排。 無法將 ExpressRoute 用於此連接。 

如果您在本地使用代理並希望將 ExpressRoute 用於複製流量，則需要在佈建服務器和處理伺服器上配置代理繞過清單。 請依照下列步驟執行：

- [從這裡](https://aka.ms/PsExec)下載 PsExec 工具以訪問系統使用者上下文。
- 通過運行以下命令列 psexec -s -i"%程式檔%__EEe_iexplore.exe"，在系統使用者上下文中打開 Internet 資源管理器
- 在 IE 中添加代理設置
- 在繞過清單中，添加 Azure 存儲 URL *.blob.core.windows.net

這將確保只有複製流量流經 ExpressRoute，而通信可以通過代理。

將虛擬機器容錯移轉到 Azure 虛擬網路之後，您就可以使用[私用對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)存取它們。 

下圖顯示組合案例：![使用 ExpressRoute 進行內部部署到 Azure 複寫](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 進行 Azure 到 Azure 複寫

Azure Site Recovery 會啟用 [Azure 虛擬機器](azure-to-azure-architecture.md)的災害復原。 視您的 Azure 虛擬機器是否使用 [Azure 受控磁碟](../virtual-machines/windows/managed-disks-overview.md)而定，複寫資料會被傳送到 Azure 儲存體帳戶或目標 Azure 區域上的複本受控磁碟。 儘管複寫端點是公用的，但不論來源虛擬網路存在於哪一個 Azure 區域，Azure VM 複寫的複寫流量預設都不會周遊網際網路。 您可以使用[自訂路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)覆寫 0.0.0.0/0 位址前置詞的 Azure 預設系統路由，並將 VM 流量導向內部部署網路虛擬設備 (NVA)，但不建議將此設定用於 Site Recovery 複寫。 如果您使用自訂路由，則應該在虛擬網路中為「儲存體」[建立虛擬網路服務端點](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，這樣一來，複寫流量就不會離開 Azure 界限。

針對 Azure VM 災害復原，根據預設，不需要 ExpressRoute 即可進行複寫。 虛擬機器容錯移轉到目標 Azure 區域之後，您就可以使用[私用對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)存取它們。 請注意，無論跨 Azure 區域的資料複製模式如何，資料傳輸價格都適用。

如果您已經使用 ExpressRoute 從內部部署資料中心連線到來源區域上的 Azure VM，則可以規劃在容錯移轉目標區域上重新建立 ExpressRoute 連線。 您可以透過新的虛擬網路連線，使用相同的 ExpressRoute 線路連線到目標區域，或利用不同的 ExpressRoute 線路和連線進行災害復原。 [這裡](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)說明各種可能案例。

您可以將 Azure 虛擬機器複寫到相同地理叢集內的任何 Azure 區域，如[這裡](../site-recovery/azure-to-azure-support-matrix.md#region-support)所詳述。 如果選擇的目標 Azure 區域與來源位於不同的地緣政治區域內，則您可能需要啟用 ExpressRoute Premium。 如需詳細資訊，請參閱 [ExpressRoute 位置](../expressroute/expressroute-locations.md)和 [ExpressRoute 價格](https://azure.microsoft.com/pricing/details/expressroute/)。

## <a name="next-steps"></a>後續步驟
- 深入了解 [ExpressRoute 線路](../expressroute/expressroute-circuit-peerings.md)。
- 深入了解 [ExpressRoute 路由網域](../expressroute/expressroute-circuit-peerings.md#peeringcompare)。
- 深入了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md)。
- 詳細瞭解[使用 ExpressRoute](azure-vm-disaster-recovery-with-expressroute.md)的 Azure 虛擬機器的災害復原。
