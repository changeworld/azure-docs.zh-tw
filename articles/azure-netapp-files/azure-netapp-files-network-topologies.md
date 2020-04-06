---
title: Azure NetApp 文件網络規劃指南 |微軟文件
description: 描述使用 Azure NetApp 檔案説明您設計有效網路體系結構的指南。
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
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667867"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>適用於 Azure NetApp Files 網路方案的指導方針

網路體繫結構規劃是設計任何應用程式基礎結構的關鍵要素。 本文可説明您為工作負荷設計有效的網路體系結構,以便從 Azure NetApp 檔案的豐富功能中獲益。

Azure NetApp 檔案卷設計為包含在 Azure 虛擬網路中稱為[委派子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)的特殊用途子網中。 因此,您可以根據需要直接從 VNet、同一區域中的對等 VNet 或虛擬網路閘道(ExpressRoute 或 VPN 閘道)從本地存取卷。 子網專用於 Azure NetApp 檔,並且沒有連接到其他 Azure 服務或 Internet。

## <a name="considerations"></a>考量  

規劃 Azure NetApp 檔網路時,應瞭解一些注意事項。

### <a name="constraints"></a>條件約束

Azure NetApp 檔案目前不支援以下功能: 

* 應用於委派子網的網路安全組 (NSG)
* 使用者定義的路由 (UDR), 位址前置為 Azure NetApp 檔案子網
* Azure NetApp 檔案介面上的 Azure 政策(例如,自訂命名策略)
* Azure NetApp 檔案流量的負載均衡器
* Azure 虛擬 WAN 不支援 Azure NetApp 檔案

以下網路限制適用於 Azure NetApp 檔案:

* 在 VNet 中使用具有 Azure NetApp 檔(包括對等 VNet)的 IP 數不能超過 1000。 我們正在努力提高這一限額,以滿足客戶規模需求。 
* 在每個 Azure 虛擬網路 (VNet) 中，都只有一個子網路可委派給 Azure NetApp Files。


### <a name="supported-network-topologies"></a>支援的網路拓撲

下表描述了 Azure NetApp 檔案支援的網路拓撲。  它還描述了不支援的拓撲的解決方法。 

|    拓撲    |    支援    |     因應措施    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    連接到本地的 VNet 中捲    |    是    |         |
|    與對等 VNet 中的卷的連線(同一區域)    |    是    |         |
|    與對等 VNet 中的卷的連線(跨區域或全域對等互連)    |    否    |    None    |
|    透過 ExpressRoute 閘道連接到卷    |    是    |         |
|    透過 ExpressRoute 閘道在分支 VNet 中從本地到卷的連線,以及與閘道傳輸的 VNet 對等互連    |    是    |        |
|    透過 VPN 閘道從本地連接到分支 VNet 中的卷    |    是    |         |
|    透過 VPN 閘道與分支 VNet 中的卷連接,以及閘道傳輸的 VNet 對等互連    |    是    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp 檔案卷的虛擬網路

本節介紹幫助您進行虛擬網路規劃的概念。

### <a name="azure-virtual-networks"></a>Azure 虛擬網路

在預配 Azure NetApp 檔卷之前,需要創建 Azure 虛擬網路 (VNet) 或使用訂閱中已存在的虛擬網路。 VNet 定義卷的網路邊界。  有關建立虛擬網路的詳細資訊,請參閱 Azure[虛擬網路文件](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。

### <a name="subnets"></a>子網路

子網將虛擬網路分割成單獨的位址空間,其中的 Azure 資源可以使用這些位址空間。  Azure NetApp 檔卷包含在稱為[委派子網](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)的特殊用途子網中。 

子網委派向 Azure NetApp 檔案服務授予顯式許可權,用於在子網中創建特定於服務的資源。  它在部署服務時使用唯一標識符。 在這種情況下,將創建一個網路介面,以啟用與 Azure NetApp 檔案的連接。

如果使用新的 VNet,則可以創建子網,並通過在[將子網委派到 Azure NetApp 檔](azure-netapp-files-delegate-subnet.md)中的說明將子網委派給 Azure NetApp 檔。 您還可以將尚未委派給其他服務的現有空子網委派給其他服務。

如果 VNet 與另一個 VNet 對等,則無法展開 VNet 位址空間。 因此,需要在 VNet 位址空間內創建新的委派子網。 如果需要擴展位址空間,則必須在擴展位址空間之前刪除 VNet 對等互連。

### <a name="udrs-and-nsgs"></a>UdRs 與 NSG

Azure NetApp 檔案的委派子網不支援使用者定義的路由 (UDR) 和網路安全組 (NSG)。

作為解決方法,您可以將 NSG 應用於允許或拒絕往來 Azure NetApp 檔委派子網的其他子網。  

## <a name="azure-native-environments"></a>Azure 本機環境

下圖說明了 Azure 本機環境:

![Azure 本機網路環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>本地 VNet

基本方案是從同一 VNet 中的虛擬機器 (VM) 創建或連接到 Azure NetApp 檔卷。 對於上圖中的 VNet 2,卷 1 在委派子網中創建,可以安裝在預設子網中的 VM 1 上。

### <a name="vnet-peering"></a>VNet 對等互連

如果同一區域中的其他 VNet 需要存取彼此的資源,則可以使用[VNet 對等互連](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)連接 VNet,以便透過 Azure 基礎結構實現安全連接。 

考慮上圖中的 VNet 2 和 VNet 3。 如果 VM 1 需要連接到 VM 2 或卷 2,或者如果 VM 2 需要連接到 VM 1 或卷 1,則需要在 VNet 2 和 VNet 3 之間啟用 VNet 對等互連。 

此外,請考慮一種方案,其中 VNet 1 與 VNet 2 對等,而 VNet 2 與同一區域中的 VNet 3 對等。 VNet 1 的資源可以連接到 VNet 2 中的資源,但除非對等 VNet 1 和 VNet 3 進行對等,否則無法連接到 VNet 3 中的資源。 

在上圖中,儘管 VM 3 可以連接到卷 1,但 VM 4 無法連接到卷 2。  原因是分支 VNet 未對等,並且_透過 VNet 對等互連不支援傳輸路由_。

## <a name="hybrid-environments"></a>混合環境

下圖說明了混合環境: 

![混合網路環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

在混合方案中,來自本地資料中心的應用程式需要訪問 Azure 中的資源。  無論是要將資料中心擴展到 Azure,還是使用 Azure 本機服務還是進行災難恢復,都是這種情況。 有關如何透過網站到網站 VPN 或 ExpressRoute 將本地多個資源連接到 Azure 中的資源的資訊,請參閱[VPN 閘道規劃選項](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)。

在混合中心輻條拓撲中,Azure 中的中心 VNet 充當與本地網路的連接中心點。 分支與集線器對等,可用於隔離工作負載。

根據設定,您可以將本地資源連接到集線器和分支中的資源。

在上圖所示的拓撲中,本地網路連接到 Azure 中的中心 VNet,並且在同一區域中有 2 個分支 VNet 與中心 VNet 對等互連。  在這種情況下,Azure NetApp 檔卷支援的連線選項如下所示:

* 本地資源 VM 1 和 VM 2 可以通過網站到網站 VPN 或 ExpressRoute 電路連接到集線器中的卷 1。 
* 本地資源 VM 1 和 VM 2 可以通過網站到網站 VPN 和區域 Vnet 對等互連連接到卷 2 或卷 3。
* 中心 VNet 中的 VM 3 可以連接到分支 VNet 1 中的卷 2 和分支 VNet 2 中的卷 3。
* 來自分支 VNet 1 的 VM 4 和分支 VNet 2 的 VM 5 可以連接到中心 VNet 中的卷 1。
* 分支 VNet 1 中的 VM 4 無法連接到分支 VNet 2 中的卷 3。 此外,分支 VNet2 中的 VM 5 無法連接到分支 VNet 1 中的卷 2。 這是這種情況,因為分支 VNet 不對等,並且_透過 VNet 對等互連不支援傳輸路由_。
* 在上述體系結構中,如果分支 VNET 中也有閘道,則透過集線器中的閘道連接的 ONF 卷的連接將丟失。 根據設計,將優先選擇分支 VNet 中的閘道,因此只有透過該閘道連接的電腦才能連接到 ANF 卷。

## <a name="next-steps"></a>後續步驟

[將子網路委派至 Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
