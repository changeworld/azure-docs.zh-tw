---
title: 案例： Vnet 的自訂隔離
titleSuffix: Azure Virtual WAN
description: 路由的案例-防止選取的 Vnet 能夠彼此聯繫
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: cherylmc
ms.openlocfilehash: 84c7b72e3ac7a5726dea38b21b14b5bd83b42340
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831017"
---
# <a name="scenario-custom-isolation-for-vnets"></a>案例： Vnet 的自訂隔離

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在 Vnet 的自訂隔離案例中，目標是要防止特定 Vnet 集能夠連線到其他特定的 Vnet 集。 不過，Vnet 必須達到 (VPN/ER/使用者 VPN) 的所有分支。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="design"></a><a name="design"></a>設計

為了找出需要多少個路由表，您可以建立連接矩陣。 在此案例中，其外觀如下所示，其中每個資料格都代表來源 (資料列) 是否可以與目的地 (資料行通訊) ：

| 寄件者 | 變更為：| *藍色 Vnet* | *Red Vnet* | *分支*|
|---|---|---|---|---|
| **藍色 Vnet** |   &#8594;|      X        |               |       X      |
| **Red Vnet**  |   &#8594;|              |       X       |       X      |
| **分支**   |   &#8594;|     X        |       X       |       X      |

上表中的每個儲存格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習到目的地前置詞 (流程的「到」端，而資料表中的資料行標頭則) 特定的流量流程。

不同的資料列模式數目將是我們在此案例中所需的路由表數目。 在此情況下，我們將會呼叫三個路由路由表， **RT_BLUE**和**RT_RED**虛擬網路，以及分支的**預設值**。 請記住，分支一律必須與預設路由表相關聯。

分支必須瞭解紅色和藍色 Vnet 的前置詞，因此所有 Vnet 都必須**RT_BLUE**或**RT_RED**) 傳播到預設 (。 Blue 和 Red Vnet 必須瞭解分支前置詞，因此分支也會傳播至這兩個路由表**RT_BLUE**和**RT_RED** 。 因此，這是最後的設計：

* 藍色虛擬網路：
  * 相關聯的路由表： **RT_BLUE**
  * 傳播至路由表： **RT_BLUE**和**預設值**
* 紅色虛擬網路：
  * 相關聯的路由表： **RT_RED**
  * 傳播至路由表： **RT_RED**和**預設值**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表： **RT_BLUE**、 **RT_RED**和**預設值**

> [!NOTE]
> 因為所有分支都必須與預設路由表相關聯，而且要傳播到相同的路由表集，所以所有分支都有相同的連線設定檔。 換句話說，Vnet 的紅色/藍色概念無法套用到分支。

> [!NOTE]
> 如果您的虛擬 WAN 部署在多個區域，您將需要在每個中樞建立**RT_BLUE**和**RT_RED**路由表，而且每個 VNet 連線的路由都必須使用傳播標籤傳播到每個虛擬中樞的路由表。

如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="workflow"></a><a name="architecture"></a>工作流程

在 [**圖 1**] 中，有藍色和紅色的 VNet 連接。

* 藍色連線的 Vnet 可以彼此接觸，也能觸及所有分支， (VPN/ER/P2S) 連接。
* 紅色 Vnet 可以彼此接觸，並觸及所有分支 (VPN/ER/P2S) 連線。

設定路由時，請考慮下列步驟。

1. 在 Azure 入口網站、 **RT_BLUE**和**RT_RED**中建立兩個自訂路由表。
2. 針對 [路由表**RT_BLUE**]，針對下列設定：
   * **關聯**：選取 [所有藍色 vnet]。
   * **傳播**：若是分支，請選取 [分支] 選項，選擇 [分支] (VPN/ER/P2S) 連接會將路由傳播到此路由表。
3. 針對**RT_RED** (VPN/ER/P2S) 的 RED vnet 和分支的路由表，重複相同的步驟。

這會導致路由設定變更，如下圖所示

**圖1**

:::image type="content" source="./media/routing-scenarios/custom-isolation/custom.png" alt-text="圖1":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
