---
title: 案例：隔離 Vnet
titleSuffix: Azure Virtual WAN
description: 路由隔離 Vnet 的案例
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: f43f17a0f3742831920836e448de3ef757f2dfa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567303"
---
# <a name="scenario-isolating-vnets"></a>案例：隔離 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要防止 Vnet 到達其他。 這就是所謂的隔離 Vnet。 VNet 內的工作負載會保持隔離，而且無法與其他 Vnet 通訊，如同任何對任何案例。 不過，需要 Vnet 才能到達所有分支（VPN、ER 和使用者 VPN）。 在此案例中，所有 VPN、ExpressRoute 和使用者 VPN 連線都會關聯至相同和一個路由表。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定此案例，請採取下列步驟來考慮：

1. 建立自訂路由表。 在此範例中，路由表是**RT_VNet**。 若要建立路由表，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 如需路由表的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
2. 當您建立**RT_VNet**路由表時，請設定下列設定：

   * **關聯**：選取您想要隔離的 vnet。
   * **傳播**：選取 [分支] 選項，[隱含分支（VPN/ER/P2S）連線] 會將路由傳播到此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="隔離 Vnet":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
