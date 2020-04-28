---
title: 網路考量 - Azure 專用 HSM | Microsoft Docs
description: 適用於「Azure 專用 HSM」部署的網路考量概觀
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "70881280"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure 專用 HSM 網路

Azure 專用 HSM 需要高度安全的網路環境。 無論是從 Azure 雲端返回到客戶的 IT 環境 (內部部署)，使用分散式應用程式，或者是用於高可用性的情境，都是如此。 Azure 網路可提供所需的安全性，共有四大不同的領域需要應對。

- 在 Azure 中的虛擬網路 (VNet) 中建立 HSM 裝置
- 連接內部部署與雲端資源，以設定和管理 HSM 裝置
- 建立並連線虛擬網路，以互連應用程式資源和 HSM 裝置
- 跨區域連線虛擬路，以相互通信，也藉此實現高可用性的情境

## <a name="virtual-network-for-your-dedicated-hsms"></a>專用 HSM 的虛擬網路

專用 HSM 會整合到虛擬網路之中，並放置在客戶擁有的 Azure 私人網路中。 如此一來，即可從虛擬機器存取裝置或是使用虛擬網路中的運算資源。  
如需進一步瞭解如何將 Azure 服務整合至虛擬網路之中，以及其所提供的功能，請參閱 [Azure 服務的虛擬網路整合](../virtual-network/virtual-network-for-azure-services.md)說明文件。

### <a name="virtual-networks"></a>虛擬網路

佈建專用 HSM 裝置之前，客戶必須先在 Azure 中建立虛擬網路，或使用已存在於客戶訂用帳戶中的虛擬網路。 虛擬網路會替專用 HSM 裝置設下安全防線。 如需更多建立虛擬網路的相關資訊，請參閱[虛擬網路說明文件](../virtual-network/virtual-networks-overview.md)。

### <a name="subnets"></a>子網路

子網路會將虛擬網路分割成位址空間，供放在其中的 Azure 資源使用。 專用 HSM 會部署至虛擬網路中的子網路。 客戶子網路中部署的每個專用 HSM 裝置會收到來自該子網路的私人 IP 位址。 部署 HSM 裝置的子網路需要明確委派至服務：Microsoft.HardwareSecurityModules/dedicatedHSMs。 如此會將特定的權限授予 HSM 服務，以便部署到子網路中。 專用 HSM 的委派會在子網路上實行某些原則限制。 目前委派的子網路上不支援網路安全性群組 (NSG) 和使用者定義路由 (UDR)。 因此，一旦將子網路委派給專用 HSM，就只能用於部署 HSM 資源。 將其他客戶資源部署至子網路均會失敗。


### <a name="expressroute-gateway"></a>ExpressRoute 閘道

目前架構需要在客戶子網路中設定 ER 閘道，其中需要設置 HSM 裝置，以便將 HSM 裝置整合到 Azure。 無法使用此 ER 閘道將內部部署位置連線到客戶在 Azure 內的 HSM 裝置。

## <a name="connecting-your-on-premises-it-to-azure"></a>將內部部署 IT 連線到 Azure

建立雲端式資源時，通常需要將私人連線連回內部部署 IT 資源。 如果是專用的 HSM，主要會用在 HSM 用戶端軟體上，以便設定 HSM 裝置，也可以用在像是備份以及從 HSM 提取記錄以供分析等活動上。 由於有多種選項，因此決策的關鍵在於連線本身的性質。  最彈性的選項是站對站 VPN，因為可能存在多個內部部署資源，而需要與 Azure 雲端中的資源 (包括 HSM) 進行安全通訊。 這需要客戶組織裝設 VPN 裝置，以便進行連線。 如果只是單一端點內部部署 (例如單一管理工作站)，則可以使用點對站 VPN 連線。
如需連線選項的更多相關資訊，請參閱 [VPN 閘道規劃選項](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

> [!NOTE]
> 目前，ExpressRoute 並非連至內部部署資源的連線選項。 另外也請注意，上述使用的 ExpressRoute 閘道不是用來連線至內部部署基礎結構。

### <a name="point-to-site-vpn"></a>點對站 VPN

要安全連線到單一端點內部部署環境，點對站虛擬私人網路是最簡單的方式。 如果您打算在 Azure 型的專用 HSM 上僅使用單個管理工作站，就相當適合。

### <a name="site-to-site-vpn"></a>站對站 VPN

站對站虛擬私人網路允許在 Azure 型專用 HSM 與內部部署 IT 之間進行安全通訊。 這麼做的原因是，HSM 的內部部署有一個備份功能，而且需要兩個連線才能執行備份。

## <a name="connecting-virtual-networks"></a>連線虛擬網路

專用 HSM 的一般部署架構是單一虛擬網路和對應子網路開始，HSM 裝置即是在其中建立和佈建。 在相同區域中，也可能有其他虛擬網路和子網路，專供會利用到專用 HSM 的應用程式元件使用。 若要在這些網路之間啟用通訊，會使用虛擬網路對等互連。

### <a name="virtual-network-peering"></a>虛擬網路對等互連

當一個區域內有多個虛擬網路需要存取彼此的資源時，可以使用虛擬網路對等互連，以建立虛擬網路之間的安全通訊通道。  虛擬網路對等互連不僅可提供安全的通訊，也可確保 Azure 資源之間的連線保持低延遲和高頻寬。

![網路對等互連](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>跨 Azure 區域連線

HSM 裝置可透過軟體程式庫，將流量重新導向其他 HSM。 如果裝置故障或喪失對裝置的存取能力，重新導向流量的功能就可派上用場。 在其他區域中部署 HSM，並啟用虛擬網路之間的跨區域通訊，可以減輕區域層級故障的情況。

### <a name="cross-region-ha-using-vpn-gateway"></a>使用 VPN 閘道進行跨區域 HA

對於全域分散式應用程式或高可用性區域容錯移轉的情況，則需要跨區域的虛擬網路連線。 透過 Azure 專用 HSM，可以使用 VPN 閘道實現高可用性，VPN 閘道可以提供兩個安全虛擬網路之間的安全通道。 如需進一步了解使用 VPN 閘道的 Vnet 對 Vnet 連線，請參閱[什麼是 VPN 閘道？](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)一文

> [!NOTE]
> 目前使用專用 HSM 進行跨區域連線方案時，無法使用全域 Vnet 對等互連，請改用 VPN 閘道。 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>後續步驟

- [常見問題集](faq.md)
- [可支援性](supportability.md)
- [高可用性](high-availability.md)
- [實體安全性](physical-security.md)
- [監視](monitoring.md)
- [部署架構](deployment-architecture.md)