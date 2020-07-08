---
title: 案例：使用自訂設定透過 Nva 路由傳送流量
titleSuffix: Azure Virtual WAN
description: 此案例可協助您使用不同的 NVA，針對網際網路系結流量，透過 Nva 來路由傳送流量。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 3fcad59bcfb75b8de6af14eae7e0bcb4962b17eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85567426"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>案例：透過 Nva 路由傳送流量-自訂（預覽）

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此 NVA （網路虛擬裝置）案例中，目標是透過 VNet 的 NVA < > 分支來路由傳送流量，並針對網際網路系結流量使用不同的 NVA。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>案例架構

[**圖 1**] 中有一個中樞 hub **1**。

* **中樞 1**直接連線到 NVA vnet **VNET 4**和**vnet 5**。

* Vnet 1、2和3與分支之間的流量（VPN/ER/P2S）應透過**VNET 4 NVA** 10.4.0.5。

* 來自 Vnet 1、2和3的所有網際網路系結流量，都預期會透過**VNET 5 NVA** 10.5.0.5。

**圖1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="圖1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定透過 NVA 的路由，以下是要考慮的步驟：

1. 為了讓網際網路系結流量透過 VNET5 進行，您需要 Vnet 1、2和3，才能直接透過 VNet 對等互連連線至 VNET 5。 您也需要在 Vnet 中設定 0.0.0.0/0 和 next 躍點10.5.0.5 的 UDR。 目前，虛擬 WAN 不允許虛擬中樞的下一個躍點 NVA 為 0.0.0.0/0。

1. 在 [Azure 入口網站中，流覽至您的虛擬中樞並建立自訂路由表， **RT_Shared**可透過從所有 Vnet 和分支連線的傳播學習路由。 在 [**圖 2**] 中，這會以空白的自訂路由表**RT_Shared**來描述。

   * **路由：** 您不需要新增任何靜態路由。

   * **關聯：** 選取 [Vnet 4] 和 [5]，這表示 Vnet 4 和5連接會與路由表**RT_Shared**建立關聯。

   * **傳播：** 因為您想讓所有分支和 VNet 連線動態地將其路由傳播到此路由表，所以請選取 [分支和所有 Vnet]。

1. 建立自訂路由表**RT_V2B** ，將流量從 vnet 1、2和3導向至分支。

   * **路由：** 為分支（VPN/ER/P2S）（[**圖 2**] 中的 10.2.0.0/16）新增一個匯總靜態路由專案，並將下一個躍點作為 VNET 4 連線。 您也需要在 VNET 4 的連接中設定分支首碼的靜態路由，並將下一個躍點指定為 VNET 4 中 NVA 的特定 IP。

   * **關聯：** 選取 [所有 Vnet 1、2和 3]。 這表示 VNet 連線1、2和3將與此路由表建立關聯，而且能夠在此路由表中學習路由（靜態和動態的傳播）。

   * **傳播：** 連接會將路由傳播至路由表。 選取 Vnet 1、2和3，可將路由從 Vnet 1、2和3傳播到此路由表。 不需要將路由從分支連線傳播到 RT_V2B，因為分支 VNet 流量會透過 VNET4 中的 NVA。
  
1. 編輯預設路由表**DefaultRouteTable**。

   所有 VPN、ExpressRoute 和使用者 VPN 連線都會與預設路由表建立關聯。 所有 VPN、ExpressRoute 和使用者 VPN 連線都會將路由傳播到相同的一組路由表。

   * **路由：** 新增 Vnet 1、2和3（[**圖 2**] 中的 10.1.0.0/16）的匯總靜態路由專案，並將下一個躍點作為 VNET 4 連線。 您也需要在 vnet 4 的連線中為 VNET 1、2和3個匯總的首碼設定靜態路由，並將下一個躍點指定為 VNET 4 中 NVA 的特定 IP。

   * **關聯：** 請確定已選取 [分支（VPN/ER/P2S）] 選項，確保內部部署分支連接與*defaultroutetable*相關聯。

   * **傳播來源：** 請確定已選取 [分支（VPN/ER/P2S）] 選項，確保內部部署連接會將路由傳播至*defaultroutetable*。

**圖2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="圖2":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
