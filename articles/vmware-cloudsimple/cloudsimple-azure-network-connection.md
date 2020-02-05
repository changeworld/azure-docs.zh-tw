---
title: VMware 解決方案（AVS）-Azure 網路連線
description: 瞭解如何將 Azure 虛擬網路連接到您的 AVS 區域網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025073"
---
# <a name="azure-network-connections-overview"></a>Azure 網路連線總覽

當您在區域中建立 AVS 服務並建立節點時，您可以：

* 要求 Azure ExpressRoute 線路，並將其附加至該區域中的 AVS 網路。
* 使用 Azure ExpressRoute 將您的 AVS 區域網路連線到您的 Azure 虛擬網路或內部部署網路。
* 從您的私人雲端環境，提供存取您的 Azure 訂用帳戶或內部部署網路中執行的服務。

ExpressRoute 連線是具有低延遲的高頻寬。

## <a name="benefits"></a>優勢

Azure 網路連線可讓您：

* 使用 Azure 做為私人雲端上虛擬機器的備份目標。
* 在您的 Azure 訂用帳戶中部署 KMS 伺服器以加密您的私用雲端 vSAN 資料存放區。
* 使用混合式應用程式，其中應用程式的 web 層會在 AVS 公用雲端中執行，而應用程式和資料庫層則會在您的私人雲端中執行。

## <a name="azure-virtual-network-connection"></a>Azure 虛擬網路連線

您可以使用 ExpressRoute 將 AVS 私人雲端連線到您的 Azure 資源。 ExpressRoute 連線可讓您從您的 AVS 私人雲端存取 Azure 訂用帳戶中執行的資源。 此連線可讓您將您的 AVS 私人雲端網路擴充至 Azure 虛擬網路。 來自 AVS 網路的路由會透過 BGP 與您的 Azure 虛擬網路交換。 如果您已設定虛擬網路對等互連，將可從您的 AVS 網路存取所有對等互連的虛擬網路。

![虛擬網路的 Azure ExpressRoute 連線](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>連至內部部署網路的 ExpressRoute 連線

您可以將現有的 Azure ExpressRoute 線路連線到您的 AVS 區域。 ExpressRoute Global 觸及功能是用來彼此連接兩個線路。 內部部署和 AVS ExpressRoute 線路之間會建立連接。 此連接可讓您將內部部署網路擴充到 AVS 私人雲端網路。 您的 AVS 網路的路由會透過 BGP 與您的內部部署網路交換。

![內部部署 ExpressRoute 連線-全球範圍](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>連線至內部部署網路和 Azure 虛擬網路

內部部署網路和 Azure 虛擬網路的連線可以與您的 AVS 網路並存。 此連線會使用 BGP 來交換內部部署網路、Azure 虛擬網路和 AVS 網路之間的路由。 當您將您的 AVS 網路連接至 Azure 虛擬網路時，如果有全球觸達連線，Azure 虛擬網路路由將會顯示在您的內部部署網路上。 路由交換會在邊緣路由器之間的 Azure 中進行。

![使用 Azure 虛擬網路連線的內部部署 ExpressRoute 連線](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要考量︰

從內部部署網路和/或從 Azure 虛擬網路連接到 AVS 網路，可讓您在所有網路之間進行路由交換。

* 內部部署網路和 AVS 網路都可以看到 Azure 虛擬網路。
* 如果您已從內部部署網路連線到您的 Azure 虛擬網路，使用全球觸達的 AVS 網路連接將允許從 AVS 網路存取虛擬網路。
* 連線的任何網路之間的子網位址**不得**重迭。
* AVS**不**會通告 ExpressRoute 連線的預設路由
* 如果您的內部部署路由器會通告預設路由，來自 AVS 網路和 Azure 虛擬網路的流量將會使用公告的預設路由。 因此，您無法使用公用 IP 位址來存取 Azure 上的虛擬機器。

## <a name="next-steps"></a>後續步驟

* [使用 ExpressRoute 將 Azure 虛擬網路連接到 AVS](virtual-network-connection.md)
* [使用 ExpressRoute 從內部部署連接到 AVS](on-premises-connection.md)
