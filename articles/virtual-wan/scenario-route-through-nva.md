---
title: 案例：透過 NVA 路由傳送流量
titleSuffix: Azure Virtual WAN
description: 透過 NVA 路由傳送流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 0716ca8f0457ca801098c97dd7a5e68751822d4d
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85848096"
---
# <a name="scenario-route-traffic-through-an-nva"></a>案例：透過 NVA 路由傳送流量

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此 NVA 案例中，目標是透過 NVA （網路虛擬裝置）將流量路由傳送至 VNet，並將 VNet 分支到分支。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="scenario-architecture"></a><a name="architecture"></a>案例架構

[**圖 1**] 中有兩個中樞;**中樞 1**和**中樞 2**。

* **中樞 1**和**中樞 2**會直接連線到 NVA Vnet **VNET 2**和**vnet 4**。

* Vnet **5**和**vnet 6**是使用**vnet 2**所對等互連。

* **Vnet 7**和**vnet 8**會與**vnet 4**對等互連。

* **Vnet 5、6、7、8**是間接輪輻，而不是直接連線至虛擬中樞。

**圖1**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="圖1":::

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定透過 NVA 的路由，以下是要考慮的步驟：

1. 識別 NVA 輪輻 VNet 連接。 在 [**圖 1**] 中，它們是**vnet 2 連接（Eastusconn）** 和**vnet 4 連線（weconn）**。

   請確定已設定 Udr：
   * 從 VNET 5 和6到 VNET 2 NVA IP
   * 從 VNET 7 和8到 VNET 4 NVA IP 
   
   您不需要直接將 VNET 5、6、7、8連線到虛擬中樞。 請確定 Vnet 5、6、7、8中的 Nsg 允許分支（VPN/ER/P2S）或 Vnet 連線到 theire 遠端 Vnet 的流量。 例如，VNET 5、6必須確保 Nsg 允許內部部署位址首碼的流量，以及連線到遠端中樞2的 Vnet 7，8。 

2. 將 Vnet 2、5、6的匯總靜態路由專案新增至中樞1的預設路由表。 

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="範例":::

3. 在 VNET 2 的虛擬網路連線中設定 Vnet 5、6的靜態路由。 若要設定虛擬網路連線的路由設定，請參閱[虛擬中樞路由](how-to-virtual-hub-routing.md#routing-configuration)。

4. 將 Vnet 4、7、8的匯總靜態路由專案新增至中樞1的預設路由表。

5. 針對中樞2的預設路由表，重複步驟2、3和4。

這會導致路由設定變更，如下圖所示

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="結果":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
