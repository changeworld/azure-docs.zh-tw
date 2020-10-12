---
title: VMware 解決方案（依 CloudSimple）-Azure 網路連線
description: 瞭解如何將 Azure 虛擬網路連線到您的 CloudSimple 區域網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77025073"
---
# <a name="azure-network-connections-overview"></a>Azure 網路連接總覽

當您在區域中建立 CloudSimple 服務並建立節點時，您可以：

* 要求 Azure ExpressRoute 線路，並將其連結至該區域中的 CloudSimple 網路。
* 使用 Azure ExpressRoute，將您的 CloudSimple 區域網路連線到您的 Azure 虛擬網路或內部部署網路。
* 提供從私人雲端環境存取 Azure 訂用帳戶或內部部署網路中執行的服務。

ExpressRoute 連接具有低延遲的高頻寬。

## <a name="benefits"></a>優點

Azure 網路連接可讓您：

* 使用 Azure 作為私用雲端上虛擬機器的備份目標。
* 在您的 Azure 訂用帳戶中部署 KMS 伺服器，以加密私用雲端 vSAN 資料存放區。
* 使用混合式應用程式，其中應用程式的 web 層會在公用雲端中執行，而應用程式和資料庫層則是在您的私人雲端中執行。

## <a name="azure-virtual-network-connection"></a>Azure 虛擬網路連接

私人雲端可以使用 ExpressRoute 連接到您的 Azure 資源。  ExpressRoute 連線可讓您從私人雲端存取 Azure 訂用帳戶中執行的資源。  此連接可讓您將私人雲端網路延伸至 Azure 虛擬網路。  來自 CloudSimple 網路的路由會透過 BGP 與您的 Azure 虛擬網路交換。  如果您已設定虛擬網路對等互連，您的 CloudSimple 網路將可存取所有對等互連的虛擬網路。

![虛擬網路的 Azure ExpressRoute 連線](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>連至內部部署網路的 ExpressRoute 連線

您可以將現有的 Azure ExpressRoute 線路連接到您的 CloudSimple 區域。 ExpressRoute 「全球接觸」功能是用來彼此連接兩個線路。  在內部部署與 CloudSimple ExpressRoute 線路之間建立連線。  此連接可讓您將內部部署網路擴充至私人雲端網路。 來自 CloudSimple 網路的路由會透過 BGP 與您的內部部署網路交換。

![內部部署 ExpressRoute 連線-全球接觸](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>連接至內部部署網路和 Azure 虛擬網路

與內部部署網路和 Azure 虛擬網路的連線可以並存于您的 CloudSimple 網路。  此連接會使用 BGP 來交換內部部署網路、Azure 虛擬網路和 CloudSimple 網路之間的路由。  當您將 CloudSimple 網路連線到您的 Azure 虛擬網路，以達到全球連線能力時，Azure 虛擬網路路由將會顯示在您的內部部署網路上。  路由交換會在邊緣路由器之間的 Azure 中進行。

![使用 Azure 虛擬網路連線的內部部署 ExpressRoute 連線](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要考量

從內部部署網路和 Azure 虛擬網路連線至 CloudSimple 網路，可讓您在所有網路之間交換路由。

* 從內部部署網路和 CloudSimple 網路都可以看到 Azure 虛擬網路。
* 如果您已從內部部署網路連線到您的 Azure 虛擬網路，則使用「全球存取範圍」連線至 CloudSimple 網路將允許從 CloudSimple 網路存取虛擬網路。
* 子網位址在任何連線的網路之間 **不得** 重迭。
* CloudSimple **不** 會通告 ExpressRoute 連線的預設路由
* 如果您的內部部署路由器會通告預設路由，來自 CloudSimple 網路和 Azure 虛擬網路的流量將會使用公告的預設路由。  因此，無法使用公用 IP 位址來存取 Azure 上的虛擬機器。

## <a name="next-steps"></a>後續步驟

* [使用 ExpressRoute 將 Azure 虛擬網路連線到 CloudSimple](virtual-network-connection.md)
* [使用 ExpressRoute 從內部部署連接到 CloudSimple](on-premises-connection.md)
