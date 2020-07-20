---
title: 案例： Vnet 的自訂隔離
titleSuffix: Azure Virtual WAN
description: 路由的案例-防止選取的 Vnet 能夠彼此聯繫
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3719956df0dce62ee69d8e306ff2cad27197616d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567307"
---
# <a name="scenario-custom-isolation-for-vnets"></a>案例： Vnet 的自訂隔離

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在 Vnet 的自訂隔離案例中，目標是要防止特定 Vnet 集能夠連線到其他特定的 Vnet 集。 不過，需要 Vnet 才能到達所有分支（VPN/ER/使用者 VPN）。

在此案例中，VPN、ExpressRoute 和使用者 VPN 連線（統稱為分支）會與相同的路由表（預設路由表）建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="architecture"></a>案例工作流程

在 [**圖 1**] 中，有藍色和紅色的 VNet 連接。

* 藍色連接的 Vnet 可以彼此連接，以及觸及所有分支（VPN/ER/P2S）連線。
* Red Vnet 可以彼此接觸，並觸及所有分支（VPN/ER/P2S）連線。

設定路由時，請考慮下列步驟。

1. 在 Azure 入口網站、 **RT_BLUE**和**RT_RED**中建立兩個自訂路由表。
2. 對於路由表**RT_BLUE**，請在下列底下：
   * **關聯**：選取所有藍色 vnet
   * **傳播**：若是分支，請選取 [分支] 選項，選擇 [分支] （VPN/ER/P2S）連線會將路由傳播到此路由表。
3. 針對 RED Vnet 和分支的**RT_RED**路由表（VPN/ER/P2S）重複相同的步驟。

這會導致路由設定變更，如下圖所示

**圖1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="圖1":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
