---
title: 案例：適用于虛擬 WAN 的 Azure 防火牆自訂路由
titleSuffix: Azure Virtual WAN
description: 在 Vnet 之間路由傳送流量的案例，但使用 Azure 防火牆進行 VNet >的網際網路/分支和分支對 VNet 流量流程
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 301bc64bee291fa25506e7f435e923be7e244cd4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267511"
---
# <a name="scenario-azure-firewall---custom"></a>案例： Azure 防火牆-自訂

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，其目標是要直接在 Vnet 之間路由傳送流量，但使用 Azure 防火牆進行 VNet 對網際網路/分支和分支對 VNet 流量。

## <a name="design"></a><a name="design"></a>設計

若要找出需要多少個路由表，您可以建立一個連接矩陣，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) 進行通訊。 此案例中的連接矩陣很簡單，但與其他案例一致，我們仍然可以查看它。

**連接矩陣**

| 從           | 變更為：      | *Vnet*      | *分支*    | *網際網路*   |
|---             |---       |---           |---            |---           |
| **Vnet**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **分支**   |   &#8594;|    AzFW      |       X       |       X      |

在上表中，"X" 代表兩個連線之間的直接連線，而不會流經虛擬 WAN 中的 Azure 防火牆，而 "AzFW" 表示流程將會通過 Azure 防火牆。 因為矩陣中有兩個不同的連接模式，所以我們需要兩個路由表，其設定如下：

* 虛擬網路：
  * 相關聯的路由表： **RT_VNet**
  * 傳播至路由表： **RT_VNet**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **預設**

> [!NOTE]
> 您可以在每個區域中建立一個具有單一安全虛擬中樞的個別虛擬 WAN 實例，然後您可以透過站對站 VPN，將每個虛擬 WAN 彼此連線。

如需虛擬中樞路由的相關資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

在此案例中，您想要透過 Azure 防火牆將流量路由傳送至 VNet 對網際網路、VNet 對分支或分支對 VNet 流量，但想要直接使用 VNet 對 VNet 流量。 如果您使用 Azure 防火牆管理員，路由設定會自動填入 **預設路由表**中。 私人流量適用于 VNet 和分支，網際網路流量會套用至 0.0.0.0/0。

VPN、ExpressRoute 和使用者 VPN 連線統稱為分支，並與相同的 (預設) 路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連接都會將路由傳播至同一組路由表。 若要設定此案例，請考慮下列步驟：

1. 建立自訂路由表 **RT_VNet**。
1. 建立路由以啟用 VNet 對網際網路和 VNet 對分支： 0.0.0.0/0，且下一個躍點指向 Azure 防火牆。 在 [傳播] 區段中，您將確認已選取 Vnet，以確保更特定的路由，進而允許 VNet 對 VNet 的直接流量。

   * 在 [關聯] 中 **：** 選取 [vnet]，這表示 vnet 會根據此路由表的路由來到達目的地。
   * 在 [傳播] 中 **：** 選取 vnet，表示 vnet 會傳播至此路由表;換句話說，更明確的路由會傳播到此路由表，藉此確保 VNet 之間的流量直接流向 VNet。

1. 將 Vnet 的匯總靜態路由新增至 **預設路由表** ，以透過 Azure 防火牆啟用分支對 VNet 流程。

   * 請記住，分支會關聯並傳播至預設路由表。
   * 分支不會傳播至 RT_VNet 路由表。 這可確保透過 Azure 防火牆的 VNet 對分支流量流動。

這會導致路由設定變更，如 [ **圖 1**] 所示。

**圖1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="圖1":::

> [!NOTE]
> 虛擬 WAN 中樞和已連線的虛擬網路應該位於相同的 Azure 區域。

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
* 如需有關如何設定虛擬中樞路由的詳細資訊，請參閱 [如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。
