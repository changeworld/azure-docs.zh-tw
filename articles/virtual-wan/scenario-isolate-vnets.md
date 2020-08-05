---
title: 案例：隔離 Vnet
titleSuffix: Azure Virtual WAN
description: 路由隔離 Vnet 的案例
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 763a13cf2ecbe845619101bc9e325cc51564260a
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/04/2020
ms.locfileid: "87553388"
---
# <a name="scenario-isolating-vnets"></a>案例：隔離 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要防止 Vnet 到達其他。 這就是所謂的隔離 Vnet。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

在此案例中，特定 VNet 內的工作負載會保持隔離，而且無法與其他 Vnet 通訊。 不過， (VPN、ER 和使用者 VPN) 的所有分支都必須要有 Vnet。 為了找出需要多少個路由表，您可以建立連接矩陣。 在此案例中，如下表所示，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行通訊) ：

| 寄件者 |   收件者 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|           |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每個儲存格都會描述虛擬 WAN 連線是否 (流程的「來源」端，資料列標頭) 學習目的地前置詞 (流程的「到」端，特定流量的斜體) 中的資料行標頭。

此連接矩陣提供兩個不同的資料列模式，可轉譯為兩個路由表。 虛擬 WAN 已經有預設路由表，所以我們需要另一個路由表。 在此範例中，我們會將路由表命名為**RT_VNET**。

Vnet 將與此**RT_VNET**路由表相關聯。 因為它們需要與分支的連接，所以分支必須傳播至**RT_VNET** (否則 vnet 不會學習) 的分支前置詞。 由於分支一律會與預設路由表相關聯，因此 Vnet 必須傳播至預設路由表。 因此，這是最後的設計：

* 虛擬網路：
  * 相關聯的路由表： **RT_VNET**
  * 傳播至路由表：**預設值**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表： **RT_VNET**和**預設值**

請注意，由於只有分支會傳播至路由表**RT_VNET**，因此這些只會是 vnet 將學習的前置詞，而不是其他 vnet 的首碼。

如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要設定此案例，請採取下列步驟來考慮：

1. 在每個中樞內建立自訂路由表。 在此範例中，路由表是**RT_VNet**。 若要建立路由表，請參閱[如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 如需路由表的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
2. 當您建立**RT_VNet**路由表時，請設定下列設定：

   * **關聯**：選取您想要隔離的 vnet。
   * **傳播**：選取 [分支] 選項，選擇 [分支] (VPN/ER/P2S) 連線會將路由傳播到此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="隔離 Vnet":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
