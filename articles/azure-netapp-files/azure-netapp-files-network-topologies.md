---
title: 適用于 Azure NetApp Files 網路規劃的指導方針 |Microsoft Docs
description: 描述可協助您使用 Azure NetApp Files 設計有效網路架構的指導方針。
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
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 12be766f36a0901079a5a26f20ea7dacc75268de
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80667867"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>適用於 Azure NetApp Files 網路方案的指導方針

網路架構規劃是設計任何應用程式基礎結構的關鍵元素。 本文可協助您設計有效的網路架構，讓您的工作負載受益于 Azure NetApp Files 的豐富功能。

Azure NetApp Files 磁片區的設計目的是要包含在 Azure 虛擬網路中稱為[委派子](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)網的特殊用途子網中。 因此，您可以視需要直接從 VNet、從相同區域中的對等互連 Vnet，或從內部部署透過虛擬網路閘道（ExpressRoute 或 VPN 閘道）存取磁片區。 子網專門用於 Azure NetApp Files，而且沒有其他 Azure 服務或網際網路的連線能力。

## <a name="considerations"></a>考量  

當您規劃 Azure NetApp Files network 時，您應該瞭解一些考慮事項。

### <a name="constraints"></a>條件約束

Azure NetApp Files 目前不支援下列功能： 

* 已套用至委派子網的網路安全性群組（Nsg）
* 位址首碼為 Azure NetApp files 子網的使用者定義路由（Udr）
* Azure NetApp Files 介面上的 azure 原則（例如，自訂命名原則）
* 適用于 Azure NetApp Files 流量的負載平衡器
* Azure 虛擬 WAN 不支援 azure NetApp Files

下列網路限制適用于 Azure NetApp Files：

* 在具有 Azure NetApp Files （包括對等互連 Vnet）的 VNet 中使用的 Ip 數目不能超過1000。 我們正努力增加此限制，以符合客戶規模需求。 
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。


### <a name="supported-network-topologies"></a>支援的網路拓撲

下表說明 Azure NetApp Files 支援的網路拓撲。  它也會描述不支援拓撲的因應措施。 

|    拓撲    |    支援    |     因應措施    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    本機 VNet 中的磁片區連線    |    是    |         |
|    連線至對等互連 VNet 中的磁片區（相同區域）    |    是    |         |
|    連線至對等互連 VNet 中的磁片區（跨區域或全域對等互連）    |    否    |    無    |
|    透過 ExpressRoute 閘道連接到磁片區    |    是    |         |
|    透過 ExpressRoute 閘道和 VNet 對等互連與閘道傳輸，從內部部署連線到輪輻 VNet 中的磁片區    |    是    |        |
|    透過 VPN 閘道從內部部署連線到輪輻 VNet 中的磁片區    |    是    |         |
|    透過 VPN 閘道和 VNet 對等互連與閘道傳輸，從內部部署連線到輪輻 VNet 中的磁片區    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>適用于 Azure NetApp Files 磁片區的虛擬網路

本節說明可協助您進行虛擬網路規劃的概念。

### <a name="azure-virtual-networks"></a>Azure 虛擬網路

布建 Azure NetApp Files 磁片區之前，您必須先建立 Azure 虛擬網路（VNet），或使用您的訂用帳戶中已存在的資源。 VNet 會定義磁片區的網路界限。  如需有關建立虛擬網路的詳細資訊，請參閱[Azure 虛擬網路檔](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子網路

子網會將虛擬網路分成不同的位址空間，以供 Azure 資源使用。  Azure NetApp Files 磁片區包含在名為「[委派的子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)」的特殊用途子網中。 

子網委派會提供明確的許可權給 Azure NetApp Files 服務，以在子網中建立服務特定資源。  它會在部署服務時使用唯一識別碼。 在此情況下，會建立網路介面來啟用 Azure NetApp Files 的連線。

如果您使用新的 VNet，您可以依照將[子網委派給 Azure Netapp files](azure-netapp-files-delegate-subnet.md)中的指示，建立子網，並將子網委派給 Azure netapp files。 您也可以委派尚未委派給其他服務的現有空白子網。

如果 VNet 與另一個 VNet 對等互連，您就無法展開 VNet 位址空間。 基於這個理由，必須在 VNet 位址空間內建立新的委派子網。 如果您需要擴充位址空間，您必須先刪除 VNet 對等互連，才能展開位址空間。

### <a name="udrs-and-nsgs"></a>Udr 和 Nsg

Azure NetApp Files 的委派子網不支援使用者定義的路由（Udr）和網路安全性群組（Nsg）。

因應措施是，您可以將 Nsg 套用至其他子網，以允許或拒絕進出 Azure NetApp Files 委派子網的流量。  

## <a name="azure-native-environments"></a>Azure 原生環境

下圖說明 Azure 原生環境：

![Azure-原生網路環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本機 VNet

基本案例是從相同 VNet 中的虛擬機器（VM）建立或連接到 Azure NetApp Files 磁片區。 對於上圖中的 VNet 2，磁片區1是在委派的子網中建立，而且可以掛接在預設子網的 VM 1 上。

### <a name="vnet-peering"></a>VNet 對等互連

如果您在相同區域中有額外的 Vnet 需要存取彼此的資源，則可以使用[VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)來連線 vnet，以啟用透過 Azure 基礎結構的安全連線能力。 

請考慮上圖中的 VNet 2 和 VNet 3。 如果 VM 1 需要連線到 VM 2 或磁片區2，或 VM 2 需要連線到 VM 1 或磁片區1，則您需要啟用 VNet 2 與 VNet 3 之間的 VNet 對等互連。 

此外，請考慮使用 vnet 2 對等互連 VNet 1 的案例，並在相同區域中使用 vnet 3 對等互連 VNet 2。 VNet 1 中的資源可以連線到 VNet 2 中的資源，但它無法連線到 VNet 3 中的資源，除非 VNet 1 和 VNet 3 已對等互連。 

在上圖中，雖然 VM 3 可以連接到磁片區1，但 VM 4 無法連線到磁片區2。  這是因為輪輻 Vnet 並未對等互連，且_VNet 對等互連不支援傳輸路由_。

## <a name="hybrid-environments"></a>混合式環境

下圖說明混合式環境： 

![混合式網路環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合式案例中，來自內部部署資料中心的應用程式需要存取 Azure 中的資源。  無論您想要將資料中心延伸至 Azure，或想要使用 Azure 原生服務或嚴重損壞修復，都是如此。 如需有關如何透過站對站 VPN 或 ExpressRoute，將內部部署的多個資源連線至 Azure 中資源的詳細資訊，請參閱[VPN 閘道規劃選項](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合式中樞輪輻拓撲中，Azure 中的中樞 VNet 會作為內部部署網路的連線中心點。 輪輻是與中樞 Vnet 對等互連，而且可以用來隔離工作負載。

視設定而定，您可以將內部部署資源連線到中樞和輪輻中的資源。

在上述的拓撲中，內部部署網路會連線到 Azure 中的中樞 VNet，而且相同區域中有2個輪輻 Vnet 與中樞 VNet 對等互連。  在此案例中，Azure NetApp Files 磁片區支援的連線選項如下所示：

* 內部部署資源 VM 1 和 VM 2 可以透過站對站 VPN 或 ExpressRoute 線路連線到中樞的磁片區1。 
* 內部部署資源 VM 1 和 VM 2 可以透過站對站 VPN 和區域 Vnet 對等互連連線到磁片區2或磁片區3。
* 中樞 VNet 中的 VM 3 可以連接到輪輻 vnet 1 中的磁片區2，而在輪輻 VNet 2 中連線到第3卷。
* 來自輪輻 vnet 1 和 VM 5 的 VM 4，可以連接到中樞 VNet 中的磁片區1。
* 輪輻 VNet 1 中的 VM 4 無法連接到輪輻 VNet 2 中的磁片區3。 此外，輪輻 VNet2 中的 VM 5 無法連接到輪輻 VNet 1 中的磁片區2。 這是因為輪輻 Vnet 不是對等互連，且_不支援透過 VNet 對等互連的傳輸路由_。
* 在上述架構中，如果輪輻 VNET 中也有閘道，則透過中樞的閘道連線到及磁片區的連線將會遺失。 根據設計，會將喜好設定提供給輪輻 VNet 中的閘道，因此只有透過該閘道連線的電腦才能連線到及磁片區。

## <a name="next-steps"></a>後續步驟

[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
