---
title: 案例：路由至共用服務 Vnet
titleSuffix: Azure Virtual WAN
description: 路由的案例-設定路由以存取共用服務 VNet，其中包含您想要讓每個 VNet 和分支存取的工作負載。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567422"
---
# <a name="scenario-route-to-shared-services-vnets"></a>案例：路由至共用服務 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要設定路由，以使用您想要讓每個 VNet 和分支（VPN/ER/P2S）存取的工作負載來存取**共用服務**VNet。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定此案例，請採取下列步驟來考慮：

1. 識別**共用服務**VNet。
2. 建立自訂路由表。 在此範例中，我們將此路由表稱為**RT_SHARED**。 如需建立路由表的步驟，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 使用下列值做為指導方針：

   * **關聯**
     * 對於**共用服務 VNet*以外*的 vnet**，請選取要隔離的 vnet。 這表示所有這些 Vnet （共用服務 VNet 除外）都可以根據 RT_SHARED 路由表的路由來連接目的地。

   * **傳播**
      * 對於**分支**，除了您可能已經選取的任何其他路由表之外，還會將路由傳播到此路由表。 在此步驟中，RT_SHARED 路由表將會從所有分支連線（VPN/ER/使用者 VPN）中學習路由。
      * 針對 [ **vnet**]，選取 [**共用服務] VNet**。 在此步驟中，RT_SHARED 路由表將會學習來自共用服務 VNet 連線的路由。

     這會導致路由設定變更，如下圖所示

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="共用服務 VNet":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
