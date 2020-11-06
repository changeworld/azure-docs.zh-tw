---
title: 關於搭配 Azure Site Recovery 使用 ExpressRoute
description: 說明如何搭配使用 Azure ExpressRoute 和 Azure Site Recovery 服務來進行災害復原和移轉。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: 99fa8d4cf8f48d0fe72da36baef20c83add438c0
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94330252"
---
# <a name="azure-expressroute-with-azure-site-recovery"></a>Azure ExpressRoute 搭配 Azure Site Recovery

Microsoft Azure ExpressRoute 可讓您透過連線提供者所提供的私人連線，將內部部署網路延伸至 Microsoft 雲端。 使用 ExpressRoute，即可和 Microsoft 雲端服務建立連線，例如 Microsoft Azure、Microsoft 365 和 Dynamics 365。

本文說明如何使用 Azure ExpressRoute 來搭配 Azure Site Recovery 以進行災害復原和移轉。

## <a name="expressroute-circuits"></a>ExpressRoute 線路

ExpressRoute 線路代表您的內部部署基礎結構與 Microsoft 雲端服務之間，透過連線提供者的邏輯連線。 您可以訂購多條 ExpressRoute 線路。 每一條線路可以位於相同或不同區域，且可透過不同的連線提供者連線到您的內部環境。 請在[這裡](../expressroute/expressroute-circuit-peerings.md)深入了解 ExpressRoute 線路。

ExpressRoute 線路有多個相關聯的路由網域。 請在[這裡](../expressroute/expressroute-circuit-peerings.md#peeringcompare)深入了解和比較 ExpressRoute 路由網域。

## <a name="on-premises-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 的內部部署到 Azure 複寫

Azure Site Recovery 能夠針對內部部署的 [Hyper-V 虛擬機器](hyper-v-azure-architecture.md)、[VMware 虛擬機器](vmware-azure-architecture.md)和[實體伺服器](physical-azure-architecture.md)進行災害復原並移轉到 Azure。 針對所有內部部署到 Azure 的案例，會將複寫資料傳送到並儲存於 Azure 儲存體帳戶。 在複寫期間，您不需支付任何虛擬機器費用。 當您容錯移轉到 Azure 時，Site Recovery 會自動建立 Azure IaaS 虛擬機器。

Site Recovery 會透過公用端點，將資料複寫到目標 Azure 區域上的 Azure 儲存體帳戶或複本受控磁片。 若要使用 ExpressRoute 進行 Site Recovery 複寫流量，您可以使用 [Microsoft 對等互連](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ，或現有的 [公用對等互連](../expressroute/about-public-peering.md) (已針對新的建立) 淘汰。 Microsoft 對等互連是建議用於複寫的路由網域。 請注意，只有在 [啟用保存庫的私人端點](hybrid-how-to-enable-replication-private-endpoints.md)時，才會透過私人對等互連來支援複寫。

確定也符合設定伺服器的 [網路需求](vmware-azure-configuration-server-requirements.md#network-requirements) 。 組態伺服器需要連線到特定 URL，才能進行 Site Recovery 複寫的協調流程。 ExpressRoute 無法用於此連線。 

如果您在內部部署使用 proxy，且想要使用 ExpressRoute 進行複寫流量，則必須在設定伺服器和進程伺服器上設定 Proxy 略過清單。 遵循下列步驟：

- 從 [這裡](/sysinternals/downloads/psexec) 下載 PsExec 工具，以存取系統使用者內容。
- 在系統使用者內容中開啟 Internet Explorer，方法是執行下列命令列 psexec-s-i "%programfiles%\Internet Explorer\iexplore.exe"
- 在 IE 中新增 proxy 設定
- 在 [略過] 清單中，新增 Azure 儲存體 URL *. blob.core.windows.net

這可確保只有複寫流量會流經 ExpressRoute，而通訊可以通過 proxy。

將虛擬機器容錯移轉到 Azure 虛擬網路之後，您就可以使用[私用對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)存取它們。 

下圖顯示組合案例：![使用 ExpressRoute 進行內部部署到 Azure 複寫](./media/concepts-expressroute-with-site-recovery/site-recovery-with-expressroute.png)

## <a name="azure-to-azure-replication-with-expressroute"></a>使用 ExpressRoute 進行 Azure 到 Azure 複寫

Azure Site Recovery 會啟用 [Azure 虛擬機器](azure-to-azure-architecture.md)的災害復原。 視您的 Azure 虛擬機器是否使用 [Azure 受控磁碟](../virtual-machines/managed-disks-overview.md)而定，複寫資料會被傳送到 Azure 儲存體帳戶或目標 Azure 區域上的複本受控磁碟。 儘管複寫端點是公用的，但不論來源虛擬網路存在於哪一個 Azure 區域，Azure VM 複寫的複寫流量預設都不會周遊網際網路。 您可以使用[自訂路由](../virtual-network/virtual-networks-udr-overview.md#custom-routes)覆寫 0.0.0.0/0 位址前置詞的 Azure 預設系統路由，並將 VM 流量導向內部部署網路虛擬設備 (NVA)，但不建議將此設定用於 Site Recovery 複寫。 如果您使用自訂路由，則應該在虛擬網路中為「儲存體」[建立虛擬網路服務端點](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)，這樣一來，複寫流量就不會離開 Azure 界限。

針對 Azure VM 災害復原，根據預設，不需要 ExpressRoute 即可進行複寫。 虛擬機器容錯移轉到目標 Azure 區域之後，您就可以使用[私用對等互連](../expressroute/expressroute-circuit-peerings.md#privatepeering)存取它們。 請注意，不論 Azure 區域之間的資料複寫模式，資料傳輸價格都適用。

如果您已經使用 ExpressRoute 從內部部署資料中心連線到來源區域上的 Azure VM，則可以規劃在容錯移轉目標區域上重新建立 ExpressRoute 連線。 您可以透過新的虛擬網路連線，使用相同的 ExpressRoute 線路連線到目標區域，或利用不同的 ExpressRoute 線路和連線進行災害復原。 [這裡](azure-vm-disaster-recovery-with-expressroute.md#fail-over-azure-vms-when-using-expressroute)說明各種可能案例。

您可以將 Azure 虛擬機器複寫到相同地理叢集內的任何 Azure 區域，如[這裡](../site-recovery/azure-to-azure-support-matrix.md#region-support)所詳述。 如果選擇的目標 Azure 區域與來源位於不同的地緣政治區域內，則您可能需要啟用 ExpressRoute Premium。 如需詳細資訊，請參閱 [ExpressRoute 位置](../expressroute/expressroute-locations.md)和 [ExpressRoute 價格](https://azure.microsoft.com/pricing/details/expressroute/)。

## <a name="next-steps"></a>後續步驟
- 深入了解 [ExpressRoute 線路](../expressroute/expressroute-circuit-peerings.md)。
- 深入了解 [ExpressRoute 路由網域](../expressroute/expressroute-circuit-peerings.md#peeringcompare)。
- 深入了解 [ExpressRoute 位置](../expressroute/expressroute-locations.md)。
- 深入瞭解 [使用 ExpressRoute 進行 Azure 虛擬機器](azure-vm-disaster-recovery-with-expressroute.md)的嚴重損壞修復。