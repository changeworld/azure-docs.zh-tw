---
title: VMware 解決方案（按雲簡單 - Azure 網路連接）
description: 瞭解如何將 Azure 虛擬網路連接到雲簡單區域網路
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025073"
---
# <a name="azure-network-connections-overview"></a>Azure 網路連接概述

在區域中創建 CloudSimple 服務並創建節點時，可以：

* 請求 Azure 快速路由電路並將其附加到該區域的 CloudSimple 網路。
* 使用 Azure ExpressRoute 將雲簡單區域網路連接到 Azure 虛擬網路或本地網路。
* 從私有雲環境提供對 Azure 訂閱中運行的服務或本地網路的訪問。

ExpressRoute 連接是高頻寬和低延遲。

## <a name="benefits"></a>優點

Azure 網路連接允許您：

* 使用 Azure 作為私有雲上虛擬機器的備份目標。
* 在 Azure 訂閱中部署 KMS 伺服器以加密私有雲 vSAN 資料存儲。
* 使用混合應用程式，其中應用程式的 Web 層在公共雲中運行，而應用程式和資料庫層在私有雲中運行。

## <a name="azure-virtual-network-connection"></a>Azure 虛擬網路連接

私有雲可以使用 ExpressRoute 連接到 Azure 資源。  通過 ExpressRoute 連接，您可以從私有雲訪問 Azure 訂閱中運行的資源。  此連接允許您將私有雲網路擴展到 Azure 虛擬網路。  雲簡單網路的路由將通過 BGP 與 Azure 虛擬網路交換。  如果配置了虛擬網路對等互連，則所有對等虛擬網路都將從 CloudSimple 網路訪問。

![Azure 快速路由連接到虛擬網路](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>與本地網路的快速路由連接

您可以將現有的 Azure 快速路由電路連接到雲簡單區域。 ExpressRoute 全域覆蓋功能用於將兩個電路相互連接。  在本地和雲簡單快速路由電路之間建立連接。  此連接允許您將本地網路擴展到私有雲網路。 來自雲簡單網路的路由將通過 BGP 與本地網路交換。

![本地快速路由連接 - 全球覆蓋](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>連接到本地網路和 Azure 虛擬網路

與本地網路和 Azure 虛擬網路的連接可以從 CloudSimple 網路共存。  該連接使用 BGP 交換本地網路、Azure 虛擬網路和 CloudSimple 網路之間的路由。  當您在存在全域覆蓋連接的情況下將 CloudSimple 網路連接到 Azure 虛擬網路時，Azure 虛擬網路路由將在本地網路上可見。  路由交換在 Azure 中發生在邊緣路由器之間。

![與 Azure 虛擬網路連接的本地快速路由連接](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>重要考量︰

從本地網路和 Azure 虛擬網路連接到 CloudSimple 網路允許在所有網路之間進行路由交換。

* Azure 虛擬網路將從本地網路和雲簡單網路可見。
* 如果從本地網路連接到 Azure 虛擬網路，則使用全域覆蓋連接到 CloudSimple 網路將允許從 CloudSimple 網路訪問虛擬網路。
* 子網位址**不得**在連接的任何網路之間重疊。
* CloudSimple**不會**通告到 ExpressRoute 連接的預設路由
* 如果本地路由器通告預設路由，則來自 CloudSimple 網路和 Azure 虛擬網路的流量將使用通告的預設路由。  因此，無法使用公共 IP 位址訪問 Azure 上的虛擬機器。

## <a name="next-steps"></a>後續步驟

* [使用快速路由將 Azure 虛擬網路連接到雲簡單](virtual-network-connection.md)
* [使用 ExpressRoute 從本地連接到雲簡單](on-premises-connection.md)
