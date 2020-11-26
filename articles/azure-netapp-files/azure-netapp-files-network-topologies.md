---
title: Azure NetApp Files 網路規劃的指導方針 | Microsoft Docs
description: 描述可協助使用 Azure NetApp Files 設計有效網路架構的指導方針。
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184479"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>適用於 Azure NetApp Files 網路方案的指導方針

網路架構規劃是設計任何應用程式基礎結構的要素。 本文可協助設計有效的網路架構，讓工作負載能夠利用 Azure NetApp Files 的豐富功能。

Azure NetApp Files 磁碟區設計成要包含在 Azure 虛擬網路內稱為[委派子網路](../virtual-network/virtual-network-manage-subnet.md)的特殊用途子網路中。 因此，您可視需要直接從 VNet、從相同區域中對等互連的 VNet，或透過虛擬網路閘道 (ExpressRoute 或 VPN 閘道) 從內部部署存取磁碟區。 此子網路會專用於 Azure NetApp Files，且不會連線到其他 Azure 服務或網際網路。

## <a name="considerations"></a>考量  

在規劃 Azure NetApp Files 網路時，您應該了解一些考量。

### <a name="constraints"></a>條件約束

Azure NetApp Files 目前不支援下列功能： 

* 套用至委派子網路的網路安全性群組 (NSG)
* 套用至委派子網路的使用者定義路由 (UDR)
* Azure NetApp Files 介面上的 Azure 原則 (例如，自訂命名原則)
* Azure NetApp Files 流量的負載平衡器
* Azure 虛擬 WAN 
* 區域備援虛擬網路閘道 (具有 Az 的閘道 SKU) 
* 主動/主動虛擬網路 GW 
* 雙重堆疊 (IPv4 和 IPv6) VNet

下列網路限制適用於 Azure NetApp Files：

* VNet 中搭配 Azure NetApp Files 使用的 IP 數目 (包括對等互連的 VNet) 不能超過 1000。 我們正在努力提高此限制，以符合客戶規模需求。 
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。


### <a name="supported-network-topologies"></a>支援的網路拓撲

下表描述 Azure NetApp Files 支援的網路拓撲。  其中也描述不受支援拓撲的因應措施。 

|    拓撲    |    是否受到支援    |     因應措施    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    連線到本機 VNet 的磁碟區    |    是    |         |
|    連線到對等互連 VNet (相同區域) 的磁碟區    |    是    |         |
|    連線到對等互連 VNet (跨區域或全域對等互連) 的磁碟區    |    否    |    None    |
|    透過 ExpressRoute 閘道，連線到磁碟區    |    是    |         |
|    透過 ExpressRoute 閘道和使用閘道傳輸的 VNet 對等互連，從內部部署連線到輪輻 VNet 的磁碟區    |    是    |        |
|    透過 VPN 閘道，從內部部署連線到輪輻 VNet 的磁碟區    |    是    |         |
|    透過 VPN 閘道和使用閘道傳輸的 VNet 對等互連，從內部部署連線到輪輻 VNet 的磁碟區    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files 磁碟區的虛擬網路

本節說明可協助進行虛擬網路規劃的概念。

### <a name="azure-virtual-networks"></a>Azure 虛擬網路

佈建 Azure NetApp Files 磁碟區之前，您必須建立 Azure 虛擬網路 (VNet)，或使用已存在於訂用帳戶中的虛擬網路。 VNet 會定義磁碟區的網路界限。  如需建立虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路文件](../virtual-network/virtual-networks-overview.md)。

### <a name="subnets"></a>子網路

子網路會將虛擬網路分割成不同的位址空間，以供其中的 Azure 資源使用。  Azure NetApp Files 磁碟區包含在稱為[委派子網路](../virtual-network/virtual-network-manage-subnet.md)的特殊用途子網路中。 

子網路委派提供明確的權限給 Azure NetApp Files 服務，以在子網路中建立服務特定資源。  其使用唯一識別碼來部署服務。 在此情況下，會建立網路介面來啟用與 Azure NetApp Files 的連線。

如果使用新的 VNet，則可遵循[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md) 中的指示，以建立子網路並將子網路委派給 Azure NetApp Files。 您也可以委派尚未委派給其他服務的現有空白子網路。

如果 VNet 與另一個 VNet 對等互連，即無法擴充 VNet 位址空間。 因此，必須在 VNet 位址空間內建立新的委派子網路。 如果需要擴充位址空間，則必須先刪除 VNet 對等互連，再擴充位址空間。

### <a name="udrs-and-nsgs"></a>UDR 和 NSG

Azure NetApp Files 的委派子網路不支援使用者定義路由 (UDR) 和網路安全性群組 (NSG)。 不過，您可將 UDR 和 NSG 套用至其他子網路，即使在與委派給 Azure NetApp Files 的子網路相同 VNet 中也一樣。

* 然後，UDR 會定義從其他子網路流向 Azure NetApp Files 委派子網路的流量。 如此有助於確保這會與使用系統路由從 Azure NetApp Files 流回其他子網路的流量一致。  
* 然後，NSG 會允許或拒絕流量進出 Azure NetApp Files 委派子網路。 

## <a name="azure-native-environments"></a>Azure 原生環境

下圖說明 Azure 原生環境：

![Azure 原生網路環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本機 VNet

基本案例是從相同 VNet 中的虛擬機器 (VM) 建立或連線到 Azure NetApp Files 磁碟區。 針對上圖中的 VNet 2，磁片區 1 是在委派子網路中建立，且可掛接在預設子網路的 VM 1 上。

### <a name="vnet-peering"></a>VNet 對等互連

如果在相同區域中有額外的 VNet 需要存取彼此資源，則可使用 [VNet 對等互連](../virtual-network/virtual-network-peering-overview.md)連線到 VNet，以啟用透過 Azure 基礎結構的安全連線。 

請考慮上圖中的 VNet 2 和 VNet 3。 如果 VM 1 需要連線到 VM 2 或磁碟區 2，或 VM 2 需要連線到 VM 1 或磁碟區 1，則需要啟用 VNet 2 與 VNet 3 之間的 VNet 對等互連。 

此外，請考慮一個案例，其中 VNet 1 與 VNet 2 對等互連，而 VNet 2 與相同區域中的 VNet 3 對等互連。 VNet 1 中資源可連線到 VNet 2 中的資源，但無法連線到 VNet 3 中的資源，除非 VNet 1 與 VNet 3 對等互連。 

在上圖中，雖然 VM 3 可連線到磁碟區 1，但 VM 4 無法連線到磁碟區 2。  這是因為輪輻 VNet 未對等互連，因此「不支援透過 VNet 對等互連進行傳輸路由」。

## <a name="hybrid-environments"></a>混合式環境

下圖說明混合式環境： 

![混合式網路環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合式案例中，來自內部部署資料中心的應用程式需要存取 Azure 中資源。  無論您想要將資料中心延伸至 Azure，或想要使用 Azure 原生服務或進行災害復原，都是如此。 如需如何透過站對站 VPN 或 ExpressRoute 將多個內部部署資源連線到 Azure 中資源的資訊，請參閱 [VPN 閘道規劃選項](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合式中樞輪輻拓撲內，Azure 的中樞 VNet 會作為內部部署網路的連線中心點。 輪輻是與中樞對等互連的 VNet，且其可用於隔離工作負載。

視設定而定，您可將內部部署資源連線到中樞和輪輻內的資源。

在上述拓撲中，內部部署網路已連線到 Azure 的中樞 VNet，且相同區域中有 2 個輪輻 VNet 與中樞 VNet 對等互連。  在此案例中，Azure NetApp Files 磁碟區支援的連線選項如下所示：

* 內部部署資源 VM 1 和 VM 2 可透過站對站 VPN 或 ExpressRoute 線路來連線到中樞的磁碟區 1。 
* 內部部署資源 VM 1 和 VM 2 可透過站對站 VPN 和區域 VNet 對等互連來連線到磁碟區 2 或磁碟區 3。
* 中樞 VNet 中 VM 3 可連線到輪輻 VNet 1 的磁碟區 2，以及輪輻 VNet 2 的磁碟區 3。
* 輪輻 VNet 1 中 VM 4 和輪輻 VNet 2 中 VM 5 可連線到中樞 VNet 的磁碟區 1。
* 輪輻 VNet 1 中 VM 4 無法連線到輪輻 VNet 2 的磁碟區 3。 此外，輪輻 VNet2 中 VM 5 無法連線到輪輻 VNet 1 的磁碟區 2。 這是因為輪輻 VNet 未對等互連，因此「不支援透過 VNet 對等互連進行傳輸路由」。
* 在上述架構中，如果輪輻 VNet 中也有閘道，則從透過中樞內閘道連線的內部部署到 ANF 磁碟區連線將會遺失。 根據設計，會偏好使用輪輻 VNet 的閘道，因此只有透過該閘道連線的電腦才能連線到 ANF 磁碟區。

## <a name="next-steps"></a>後續步驟

[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)