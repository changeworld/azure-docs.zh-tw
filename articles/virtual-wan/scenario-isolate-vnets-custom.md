---
title: 案例： Vnet 的自訂隔離
titleSuffix: Azure Virtual WAN
description: 路由的案例-防止選取的 Vnet 無法彼此聯繫
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.openlocfilehash: 0a3665f1719c7a5f8ed9bd6acf518b642e06320d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400053"
---
# <a name="scenario-custom-isolation-for-vnets"></a>案例： Vnet 的自訂隔離

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在 Vnet 的自訂隔離案例中，其目標是要防止特定的 Vnet 集合能夠觸及其他特定的 Vnet 集。 不過， (VPN/ER/使用者 VPN) 的所有分支都必須要有 Vnet。 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

為了找出需要多少個路由表，您可以建立連接矩陣。 在此案例中，它看起來會如下所示，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行) ：

| 寄件者 | 變更為：| *藍色 Vnet* | *Red Vnet* | *分支*|
|---|---|---|---|---|
| **藍色 Vnet** |   &#8594;|      X        |               |       X      |
| **Red Vnet**  |   &#8594;|              |       X       |       X      |
| **分支**   |   &#8594;|     X        |       X       |       X      |

上表中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習目的地前置詞 (流程的「到」端、在資料表中為斜體的資料行標頭) 特定的流量，其中「X」表示連線是由虛擬 WAN 提供。

不同資料列模式的數目將是此案例中所需的路由表數目。 在此情況下，我們將會針對虛擬網路呼叫 **RT_BLUE** 和 **RT_RED** 的三個路由路由表，並為分支的 **預設值** 。 請記住，分支永遠必須與預設路由表相關聯。

分支將需要從 Red 和 Blue Vnet 學習前置詞，因此所有 Vnet 都必須 **RT_BLUE** 或 **RT_RED**) 傳播至預設 (。 Blue 和 Red Vnet 需要學習分支前置詞，因此分支也會傳播至路由表 **RT_BLUE** 和 **RT_RED** 。 因此，這是最後的設計：

* 藍色虛擬網路：
  * 相關聯的路由表： **RT_BLUE**
  * 傳播至路由表： **RT_BLUE** 和 **預設值**
* 紅色虛擬網路：
  * 相關聯的路由表： **RT_RED**
  * 傳播至路由表： **RT_RED** 和 **預設值**
* 分支：
  * 相關聯的路由表： **預設值**
  * 傳播至路由表： **RT_BLUE**、 **RT_RED** 和 **預設值**

> [!NOTE]
> 由於所有分支都必須與預設路由表相關聯，而且要傳播至同一組路由表，因此所有分支都會有相同的連線設定檔。 換句話說，Vnet 的 Red/Blue 概念無法套用至分支。

> [!NOTE]
> 如果您的虛擬 WAN 部署于多個區域，您將需要在每個中樞內建立 **RT_BLUE** 和 **RT_RED** 路由表，而且每個 VNet 連線的路由都必須使用傳播標籤傳播到每個虛擬中樞內的路由表。

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="architecture"></a>工作流程

在 [ **圖 1**] 中，有 Blue 和 Red VNet 連接。

* 藍色連接的 Vnet 可以彼此聯繫，也可以觸及所有分支 (VPN/ER/P2S) 連線。
* Red Vnet 可以彼此聯繫，也可以觸及所有分支 (VPN/ER/P2S) 連線。

設定路由時，請考慮下列步驟。

1. 在 Azure 入口網站、 **RT_BLUE** 和 **RT_RED**中建立兩個自訂路由表。
2. 針對路由表 **RT_BLUE**，請進行下列設定：
   * **關聯**：選取所有 Blue vnet。
   * **傳播**：若為分支，請選取分支的選項，意指分支 (VPN/ER/P2S) 連接會將路由傳播至此路由表。
3. 針對 Red Vnet 和分支的 **RT_RED** 路由表重複相同步驟， (VPN/ER/P2S) 。

這會導致路由設定變更，如下圖所示

**圖1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="圖1":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
