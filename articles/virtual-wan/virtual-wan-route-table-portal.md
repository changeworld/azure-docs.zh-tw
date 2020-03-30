---
title: 虛擬 WAN：創建虛擬中心路由表到 NVA：Azure 門戶
description: 虛擬 WAN 虛擬中心路由表，用於使用門戶將流量引導到網路虛擬裝置。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a route table using the portal.
ms.openlocfilehash: 0807b535adc45093b439dba5ab8a0ea26b2a0721
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402941"
---
# <a name="create-a-virtual-wan-hub-route-table-for-nvas-azure-portal"></a>為 NVA 創建虛擬 WAN 中心路由表：Azure 門戶

本文介紹如何通過網路虛擬裝置 （NVA） 引導流量從連接到虛擬 WAN 集線器的分支（本地網站）引導到分支虛擬網路 （VNet）。

![虛擬 WAN 的圖表](./media/virtual-wan-route-table/vwanroute.png)

## <a name="before-you-begin"></a>開始之前

請確認您已符合下列條件：

*  您擁有網路虛擬裝置 （NVA）。 網路虛擬裝置是您選擇的協力廠商軟體，通常從虛擬網路中的 Azure 應用商店預配。

    * 必須將私人 IP 位址分配給 NVA 網路介面。

    * NVA 未部署在虛擬中心中。 它必須部署在單獨的虛擬網路中。

    *  NVA 虛擬網路可能已連接到一個或多個虛擬網路。 在本文中，我們將 NVA 虛擬網路稱為"間接分支 VNet"。 這些虛擬網路可以使用 VNet 對等互連連接到 NVA VNet。 VNet 對等互連連結由上圖中的黑色箭頭在 VNet 1、VNet 2 和 NVA VNet 之間進行描繪。
*  您已經創建了兩個虛擬網路。 它們將用作分支 VNet。

    * VNet 分支位址空間為：VNet1：10.0.2.0/24 和 VNet2：10.0.3.0/24。 如果需要有關如何創建虛擬網路的資訊，請參閱[創建虛擬網路](../virtual-network/quick-create-portal.md)。

    * 確保任何 VNet 中沒有虛擬網路閘道。

    * VNet 不需要閘道子網。

## <a name="1-sign-in"></a><a name="signin"></a>1. 登錄

從瀏覽器導航到 Azure[門戶](https://portal.azure.com)並使用 Azure 帳戶登錄。

## <a name="2-create-a-virtual-wan"></a><a name="vwan"></a>2. 創建虛擬廣域網路

建立虛擬 WAN。 使用以下示例值：

* **虛擬 WAN 名稱：** 我的虛擬 WAN
* **資源組：testRG**
* **位置：** 美國西部

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="3-create-a-hub"></a><a name="hub"></a>3. 創建集線器

創建集線器。 使用以下示例值：

* **位置：** 美國西部
* **姓名：** 韋斯特舒布
* **集線器專用位址空間：** 10.0.1.0/24

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="4-create-and-apply-a-hub-route-table"></a><a name="route"></a>4. 創建並應用中心路由表

使用中心路由表更新中心。 使用以下示例值：

* **分支 VNet 位址空間**：（VNet1 和 VNet2） 10.0.2.0/24 和 10.0.3.0/24
* **DMZ NVA 網路介面私人 IP 位址**：10.0.4.5

1. 導航到虛擬廣域網路。
2. 按一下要為其創建路由表的中心。
3. 按一下 **..."，** 然後按一下 **"編輯虛擬中心**"。
4. 在 **"編輯虛擬中心集線器**"頁上，向下滾動並選擇"**使用"表進行路由**核取方塊。
5. 在 **"如果目標首碼是**列"中，添加位址空格。 在"**發送到下一個躍點**"列中，添加 DMZ NVA 網路介面私人 IP 位址。
6. 按一下 **"確認**"以使用路由表設置更新中心資源。

## <a name="5-create-the-vnet-connections"></a><a name="connections"></a>5. 創建 VNet 連接

從每個間接分支 VNet（VNet1 和 VNet2） 創建到集線器的虛擬網路連接。 上圖中的藍色箭頭描述了這些虛擬網路連接。 然後，創建從 NVA VNet 到集線器的 VNet 連接（圖中的黑色箭頭）。

 對於此步驟，可以使用以下值：

| 虛擬網路名稱| 連接名稱|
| --- | --- |
| VNet1 | 測試連接1 |
| VNet2 | 測試連接2 |
| NVAVNet | 測試連接3 |

對要連接的每個虛擬網路重複以下過程。

1. 在虛擬 WAN 頁面上，按一下 [虛擬網路連線]****。
2. 在 [虛擬網路連線] 頁面上，按一下 [+ 新增連線]****。
3. 在 [新增連線]**** 頁面上，填寫下列欄位︰

    * **名稱** - 為您的連線命名。
    * **中樞** - 選取要與此連線產生關聯的中樞。
    * **訂用帳戶** - 請確認訂用帳戶。
    * **虛擬網路** - 選取要與此中樞連線的虛擬網路。 虛擬網路不能有現有的虛擬網路閘道。
4. 按一下 [確定]**** 來建立連線。

## <a name="next-steps"></a>後續步驟

若要深入了解虛擬 WAN，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)頁面。
