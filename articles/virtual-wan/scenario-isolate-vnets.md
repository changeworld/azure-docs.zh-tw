---
title: 案例：隔離 Vnet
titleSuffix: Azure Virtual WAN
description: 路由隔離 Vnet 的案例
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: f725932b30fad062123d6c752f2d563b84f98b2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91267630"
---
# <a name="scenario-isolating-vnets"></a>案例：隔離 Vnet

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此案例中，目標是要避免 Vnet 無法連絡其他。 這就是所謂的隔離 Vnet。 如需虛擬中樞路由的相關資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

在此案例中，特定 VNet 內的工作負載會保持隔離，而且無法與其他 Vnet 通訊。 不過， (VPN、ER 和使用者 VPN) 的所有分支都需要 Vnet。 為了找出需要多少個路由表，您可以建立連接矩陣。 在此案例中，它看起來會像下表，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) ：

| 寄件者 |   收件者 |  *Vnet* | *分支* |
| -------------- | -------- | ---------- | ---|
| VNets     | &#8594;|           |     X    |
| 分支   | &#8594;|    X     |     X    |

上表中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料列標頭) 學習目的地首碼 (流程的「到」端、以斜體) 的資料行標頭來指定流量，其中「X」表示虛擬 WAN 提供連線能力。

此連接矩陣可提供兩個不同的資料列模式，以轉譯成兩個路由表。 虛擬 WAN 已經有預設路由表，因此需要另一個路由表。 在此範例中，我們會將路由表命名為 **RT_VNET**。

Vnet 將會與此 **RT_VNET** 路由表建立關聯。 因為它們需要連接到分支，所以分支必須傳播至 **RT_VNET** (否則 vnet 不會學習) 的分支首碼。 因為分支一律會與預設路由表相關聯，所以 Vnet 必須傳播到預設路由表。 因此，這是最後的設計：

* 虛擬網路：
  * 相關聯的路由表： **RT_VNET**
  * 傳播至路由表： **預設**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_VNET** 和 **預設值**

請注意，由於只有分支會傳播至路由表 **RT_VNET**，這些只是 vnet 將學習的前置詞，而不是其他 vnet 的首碼。

如需虛擬中樞路由的相關資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="workflow"></a>工作流程

若要設定此案例，請考慮下列步驟：

1. 在每個中樞內建立自訂路由表。 在此範例中，路由表是 **RT_VNet**。 若要建立路由表，請參閱 [如何設定虛擬中樞路由](how-to-virtual-hub-routing.md)。 如需路由表的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
2. 當您建立 **RT_VNet** 路由表時，請設定下列設定：

   * **關聯**：選取您想要隔離的 vnet。
   * **傳播**：選取分支的選項，意指分支 (VPN/ER/P2S) 連接會將路由傳播至此路由表。

:::image type="content" source="./media/routing-scenarios/isolated/isolated-vnets.png" alt-text="隔離 Vnet":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
