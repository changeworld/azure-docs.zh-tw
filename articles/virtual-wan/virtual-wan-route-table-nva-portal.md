---
title: 虛擬 WAN：建立 NVA 的虛擬中樞路由表：Azure 入口網站
description: 虛擬 WAN 虛擬中樞路由表，可使用入口網站將流量引導到網路虛擬設備。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 8de7ad2808e5319819410b3125472e28496647b0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267137"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>建立 NVA 的虛擬 WAN 中樞路由表：Azure 入口網站

此文章說明如何透過網路虛擬設備 (NVA)，將流量從連線至虛擬 WAN 中樞的分支 (內部部署網站) 引導到輪輻虛擬網路 (VNet)。

![虛擬 WAN 的圖表](./media/virtual-wan-route-table-nva/vwanroute.png)

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

*  您有網路虛擬設備 (NVA)。 網路虛擬設備是可選用的第三方軟體，這通常是從 Azure Marketplace 佈建到虛擬網路。

    * 私人 IP 位址必須指派給 NVA 網路介面。

    * NVA 不會部署在虛擬中樞內。 其必須部署在不同的虛擬網路中。

    *  NVA 虛擬網路可以有一或多個虛擬網路與其連接。 在此文章中，我們將 NVA 虛擬網路稱為「間接輪輻 VNet」。 這些虛擬網路可以使用 VNet 對等互連連線到 NVA VNet。 VNet 對等互連連結是由上圖中 VNet 1、VNet 2 與 NVA VNet 之間的黑色箭號所描述。
*  您已建立兩個虛擬網路。 其會作為輪輻 VNet 使用。

    * VNet 輪輻位址空間是：VNet1：10.0.2.0/24 與 VNet2：10.0.3.0/24。 如果您需要如何建立虛擬網路的詳細資訊，請參閱[建立虛擬網路](../virtual-network/quick-create-portal.md)。

    * 請確定任何 VNet 中都沒有虛擬網路閘道。

    * VNet 不需要閘道子網路。

## <a name="1-sign-in"></a><a name="signin"></a>1.登入

透過瀏覽器瀏覽至 [Azure 入口網站](https://portal.azure.com) ，並使用您的 Azure 帳戶登入。

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2.建立虛擬 WAN

建立虛擬 WAN。 請使用下列範例值：

* **虛擬 WAN 名稱：** myVirtualWAN
* **資源群組：** testRG
* **位置：** 美國西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3.建立中樞

建立中樞。 請使用下列範例值：

* **位置：** 美國西部
* **名稱：** westushub
* **中樞私人位址空間：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4.建立並套用中樞路由表

使用中樞路由表來更新中樞。 請使用下列範例值：

* **輪輻 VNet 位址空間：** (VNet1 與 VNet2) 10.0.2.0/24 與 10.0.3.0/24
* **DMZ NVA 網路介面私人 IP 位址：** 10.0.4.5

1. 瀏覽至您的虛擬 WAN。
2. 按一下您要建立路由表的中樞。
3. 按一下 [...]，然後按一下 [編輯虛擬中樞]。
4. 在 [編輯虛擬中樞] 頁面上，向下捲動並選取 [使用路由資料表] 核取方塊。
5. 在 [若目的地前置詞為] 資料行中，新增位址空間。 在 [傳送到下一個躍點] 資料行中，新增 DMZ NVA 網路介面私人 IP 位址。

   > [!NOTE]
   > DMZ NVA 網路適用於本機中樞。
   
6. 按一下 [確認] 以使用路由表設定來更新中樞資源。

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5.建立 VNet 連線

建立從每個間接輪輻 VNet (VNet1 與 VNet2) 到中樞的虛擬網路連線。 這些虛擬網路連線是由上圖中的藍色箭號所描述。 然後，從 NVA VNet 建立 VNet 至中樞的連線 (圖中的黑色箭號)。

 針對此步驟，您可以使用下列值：

| 虛擬網路名稱| 連接名稱|
| --- | --- |
| VNet1 | testconnection1 |
| VNet2 | testconnection2 |
| NVAVNet | testconnection3 |

針對您想要連線的每個虛擬網路，重複執行下列程序。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]。
3. 在 [新增連線] 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定] 來建立連線。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
