---
title: 案例：適用于虛擬 WAN 的 Azure 防火牆自訂路由
titleSuffix: Azure Virtual WAN
description: 在 Vnet 之間直接路由傳送流量的案例，但使用適用于 VNet 的 Azure 防火牆->的網際網路/分支和分支到 VNet 流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: cdaa594a87d960688638591e6edd525aa3b048f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567442"
---
# <a name="scenario-azure-firewall---custom"></a>案例： Azure 防火牆-自訂

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是直接路由 Vnet 之間的流量，但使用適用于 VNet 對網際網路/分支和分支對 VNet 流量流程的 Azure 防火牆。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

在此案例中，您想要透過 Azure 防火牆來路由傳送 VNet 對網際網路、VNet 對分支或分支對 VNet 流量的流量，但想要直接進入 VNet 對 VNet 流量。 如果您使用 Azure 防火牆管理員，路由設定會自動填入**預設路由表**中。 私人流量適用于 VNet 和分支，網際網路流量則適用于 0.0.0.0/0。

VPN、ExpressRoute 和使用者 VPN 連線統稱為「分支」，並與相同的（預設）路由表產生關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。 若要設定此案例，請採取下列步驟來考慮：

1. 建立自訂路由表**RT_VNET**。
1. 建立路由來啟動 VNet 對網際網路和 VNet 對分支： 0.0.0.0/0，並將下一個躍點指向 Azure 防火牆。 在 [傳播] 區段中，您會確定已選取 [Vnet]，以確保更特定的路由，因而允許 VNet 對 VNet 直接流量流程。

   * 在 [關聯] 中 **：** 選取 [vnet]，這表示 vnet 會根據此路由表的路由到達目的地。
   * 在 [傳播] 中 **：** 選取 vnet，這表示 vnet 會傳播到此路由表;換句話說，更特定的路由會傳播到此路由表，以確保 VNet 之間的流量直接流向 VNet。

1. 將 Vnet 的匯總靜態路由新增至**預設路由表**，以透過 Azure 防火牆啟動分支對 VNet 流程。 

   * 請記住，分支會建立關聯並傳播至預設路由表。
   * 分支不會傳播到 RT_VNET 的路由表。 這可確保透過 Azure 防火牆的 VNet 對分支流量流動。

這會導致路由設定變更，如 [**圖 1**] 所示。

**圖1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="圖1":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
* 如需有關如何設定虛擬中樞路由的詳細資訊，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。
