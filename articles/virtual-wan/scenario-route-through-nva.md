---
title: '案例：透過網路虛擬裝置路由傳送流量 (NVA) '
titleSuffix: Azure Virtual WAN
description: 透過 NVA 路由傳送流量
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: a8bed6c46b0660d5bf43863a5c7aaf4eeaf7e26f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853195"
---
# <a name="scenario-route-traffic-through-an-nva"></a>案例：透過 NVA 路由傳送流量

使用虛擬 WAN 虛擬中樞路由時，有很多可用的案例。 在此 NVA 案例中，目標是透過 NVA (網路虛擬) 設備，將流量路由傳送至 VNet，並將 VNet 分支到分支。 如需虛擬中樞路由的相關資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

> [!NOTE]
> 某些路由功能可能仍在推出。如果您的區域尚未進行首度發行，請在此同時使用這些版本文章中的步驟：
>* [Azure 入口網站篇文章](virtual-wan-route-table-nva-portal.md)
>* [PowerShell 文章](virtual-wan-route-table-nva.md)
>

## <a name="design"></a><a name="design"></a>設計

在此案例中，我們將使用命名慣例：

* 適用于虛擬網路的「NVA Vnet」，其中使用者已部署 NVA，並已將其他虛擬網路連接成**連接矩陣**中的輪輻 (vnet 2 和 vnet 4，如下) 。
* 連線到 NVA VNet (VNet 5、VNet 6、VNet 7 和 VNet 8 的虛擬網路「NVA 輪輻」，) 下方的**連接矩陣**。
* 連線至虛擬 WAN 的虛擬網路「非 NVA Vnet」，其連接**矩陣**中沒有 NVA 或其他 vnet 對等互連 (VNet 1 和 vnet 3，) 下方。
* 適用于 Microsoft 管理之虛擬 WAN 中樞的「中樞」，NVA Vnet 會在其中連線。 NVA 輪輻 Vnet 不需要連線到虛擬 WAN 中樞，只能連接到 NVA Vnet。

下列連接矩陣摘要說明此案例中支援的流程：

**連接矩陣**

| 寄件者             | 變更為：|   *NVA 輪輻*|*NVA Vnet*|*非 NVA Vnet*|*分支*|
|---|---|---|---|---|---|
| **NVA 輪輻**   | &#8594; | 0/0 UDR  |  對等互連 |   0/0 UDR    |  0/0 UDR  |
| **NVA Vnet**    | &#8594; |   靜態 |      X   |        X     |      X    |
| **非 NVA Vnet**| &#8594; |   靜態 |      X   |        X     |      X    |
| **分支**     | &#8594; |   靜態 |      X   |        X     |      X    |

連線矩陣中的每個資料格都會描述虛擬 WAN 連線是否 (流程的「來源」端、資料表中的資料列標頭) 學習到目的地前置詞 (流程的「到」端，而資料表中的資料行標頭則) 特定的流量流程。 請考慮下列事項：

* NVA 輪輻不受虛擬 WAN 管理。 因此，與其他 Vnet 或分支通訊的機制會由使用者維護。 NVA VNet 的連線是由 VNet 對等互連所提供，而預設路由為 0.0.0.0/0，指向 NVA 做為下一個躍點應涵蓋連線至網際網路、其他輪輻和分支
* NVA Vnet 會知道自己的 NVA 輪輻，而不是與連接到其他 NVA Vnet 的 NVA 輪輻有關。 例如，在 [表 1] 中，VNet 2 知道 VNet 5 和 VNet 6，但不適用於 VNet 7 和 VNet 8 等其他分支。 必須要有靜態路由，才能將其他輪輻首碼插入 NVA Vnet
* 同樣地，分支和非 NVA 的 Vnet 也不會知道任何 NVA 輪輻，因為 NVA 輪輻並未連接到 VWAN 中樞。 因此，這裡也會需要靜態路由。

考慮到 NVA 輪輻不是由虛擬 WAN 管理，所有其他資料列都會顯示相同的連線模式。 因此，單一路由表 () 將會執行的預設值：

* 虛擬網路 (非中樞 Vnet 和使用者中樞 Vnet) ：
  * 關聯的路由表：**預設值**
  * 傳播至路由表：**預設值**
* 網點
  * 關聯的路由表：**預設值**
  * 傳播至路由表：**預設值**

不過，在此案例中，我們需要考慮要設定的靜態路由。 每個靜態路由都會有兩個元件，虛擬 WAN 中樞中的一個部分會告知虛擬 WAN 元件要用於每個輪輻的連線，而該特定連線中的另一個是指向指派給 NVA (的具體 IP 位址，或位於多個 Nva) 前方的負載平衡器，如 [**圖 1** ] 所示：

**圖1**

:::image type="content" source="media/routing-scenarios/nva/nva-static-concept.png" alt-text="圖1":::

如此一來，我們在預設資料表中所需的靜態路由，可將流量傳送至 NVA VNet 後方的 NVA 輪輻，如下所示：

| 描述 | 路由表 | 靜態路由              |
| ----------- | ----------- | ------------------------- |
| VNet 2       | 預設     | 10.2.0.0/16-> eastusconn |
| VNet 4       | 預設     | 10.4.0.0/16-> weconn     |

現在虛擬 WAN 知道要將封包傳送到哪個連線，但連線需要知道接收這些封包時該怎麼做：這是使用連接路由表的位置。 在這裡，我們將使用較短的前置詞 (/24，而不是較長/16 的) ，以確保這些路由的優先順序高於從 NVA Vnet (VNet 2 和 VNet 4) 匯入的路由：

| 描述 | Connection | 靜態路由            |
| ----------- | ---------- | ----------------------- |
| VNet 5       | eastusconn | 10.2.1.0/24-> 10.2.0。5 |
| VNet 6       | eastusconn | 10.2.2.0/24-> 10.2.0。5 |
| VNet 7       | weconn     | 10.4.1.0/24-> 10.4.0。5 |
| VNet 8       | weconn     | 10.4.2.0/24-> 10.4.0。5 |

現在 NVA Vnet，非 NVA Vnet 和分支知道如何觸及所有 NVA 輪輻。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="architecture"></a><a name="architecture"></a>架構

[**圖 2**] 中有兩個中樞：**Hub1**和**Hub2**。

* **Hub1**和**Hub2**會直接連線至 NVA Vnet **VNet 2**和**vnet 4**。

* Vnet **5**和**vnet 6**是使用**vnet 2**所對等互連。

* **Vnet 7**和**vnet 8**會與**vnet 4**對等互連。

* **Vnet 5、6、7、8**是間接輪輻，而不是直接連線至虛擬中樞。

**圖2**

:::image type="content" source="./media/routing-scenarios/nva/nva.png" alt-text="圖2" lightbox="./media/routing-scenarios/nva/nva.png":::

## <a name="scenario-workflow"></a><a name="workflow"></a>案例工作流程

若要設定透過 NVA 的路由，以下是要考慮的步驟：

1. 識別 NVA 輪輻 VNet 連接。 在 [**圖 2**] 中，它們是**vnet 2 連線 (Eastusconn) **和**vnet 4 連接 (weconn) **。

   請確定已設定 Udr：
   * 從 VNet 5 和 VNet 6 到 VNet 2 NVA IP
   * 從 VNet 7 和 VNet 8 到 VNet 4 NVA IP 
   
   您不需要直接將 Vnet 5、6、7、8連線到虛擬中樞。 請確定 Vnet 5、6、7、8中的 Nsg 允許分支 (VPN/ER/P2S) 或 Vnet 連接到遠端 Vnet 的流量。 例如，Vnet 5、6必須確保 Nsg 允許內部部署位址首碼的流量，以及連線到遠端中樞2的 Vnet 7，8。

2. 將 Vnet 2、5、6的匯總靜態路由專案新增至中樞1的預設路由表。

   :::image type="content" source="./media/routing-scenarios/nva/nva-static-expand.png" alt-text="範例":::

3. 在 VNet 2 的虛擬網路連線中設定 Vnet 5、6的靜態路由。 若要設定虛擬網路連線的路由設定，請參閱[虛擬中樞路由](how-to-virtual-hub-routing.md#routing-configuration)。

4. 將 Vnet 4、7、8的匯總靜態路由專案新增至中樞1的預設路由表。

5. 針對中樞2的預設路由表，重複步驟2、3和4。

這會導致路由設定變更，如下面的 [**圖 3**] 所示。

**圖3**

   :::image type="content" source="./media/routing-scenarios/nva/nva-result.png" alt-text="圖3" lightbox="./media/routing-scenarios/nva/nva-result.png":::

## <a name="next-steps"></a>後續步驟

* 如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
