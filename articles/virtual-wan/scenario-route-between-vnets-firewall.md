---
title: 案例：適用于虛擬 WAN 的 Azure 防火牆自訂路由
titleSuffix: Azure Virtual WAN
description: 在 Vnet 之間直接路由傳送流量的案例，但使用適用于 VNet 的 Azure 防火牆->的網際網路/分支和分支到 VNet 流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f48b30e0e4e76a4cf4c855008776f6b7541ad5a2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850641"
---
# <a name="scenario-azure-firewall---custom"></a>案例： Azure 防火牆-自訂

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是直接路由 Vnet 之間的流量，但使用適用于 VNet 對網際網路/分支和分支對 VNet 流量流程的 Azure 防火牆。

## <a name="design"></a><a name="design"></a>設計

為了找出需要多少個路由表，您可以建立一個連接矩陣，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) 進行通訊。 此案例中的連線矩陣很簡單，但與其他案例一致，我們仍然可以查看它。

**連接矩陣**

| 寄件者           | 變更為：      | *Vnet*      | *分支*    | *網際網路*   |
|---             |---       |---           |---            |---           |
| **Vnet**      |   &#8594;|     X        |     AzFW      |     AzFW     |
| **分支**   |   &#8594;|    AzFW      |       X       |       X      |

在上表中，「X」代表兩個連線之間的直接連線，而不會流經虛擬 WAN 中的 Azure 防火牆，而「AzFW」則表示流程將會經過 Azure 防火牆。 因為矩陣中有兩個不同的連線模式，所以我們需要兩個路由表，其將設定如下：

* 虛擬網路：
  * 相關聯的路由表： **RT_VNet**
  * 傳播至路由表： **RT_VNet**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表：**預設值**


如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

在此案例中，您想要透過 Azure 防火牆來路由傳送 VNet 對網際網路、VNet 對分支或分支對 VNet 流量的流量，但想要直接進入 VNet 對 VNet 流量。 如果您使用 Azure 防火牆管理員，路由設定會自動填入**預設路由表**中。 私人流量適用于 VNet 和分支，網際網路流量則適用于 0.0.0.0/0。

VPN、ExpressRoute 和使用者 VPN 連線統稱為「分支」，並與相同的 (預設) 路由表相關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。 若要設定此案例，請採取下列步驟來考慮：

1. 建立自訂路由表**RT_VNet**。
1. 建立路由來啟動 VNet 對網際網路和 VNet 對分支： 0.0.0.0/0，並將下一個躍點指向 Azure 防火牆。 在 [傳播] 區段中，您會確定已選取 [Vnet]，以確保更特定的路由，因而允許 VNet 對 VNet 直接流量流程。

   * 在 [關聯] 中 **：** 選取 [vnet]，表示 vnet 會根據此路由表的路由到達目的地。
   * 在 [傳播] 中 **：** 選取 vnet，這表示 vnet 會傳播到此路由表;換句話說，更特定的路由會傳播到此路由表，以確保 VNet 之間的流量直接流向 VNet。

1. 將 Vnet 的匯總靜態路由新增至**預設路由表**，以透過 Azure 防火牆啟動分支對 VNet 流程。

   * 請記住，分支會建立關聯並傳播至預設路由表。
   * 分支不會傳播到 RT_VNet 的路由表。 這可確保透過 Azure 防火牆的 VNet 對分支流量流動。

這會導致路由設定變更，如 [**圖 1**] 所示。

**圖1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="圖1":::

> [!NOTE]
> 虛擬 WAN 中樞和已連線的虛擬網路應位於相同的 Azure 區域。

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
* 如需有關如何設定虛擬中樞路由的詳細資訊，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。
