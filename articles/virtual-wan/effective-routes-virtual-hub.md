---
title: 查看虛擬中樞的有效路由： Azure 虛擬 WAN |Microsoft Docs
description: 如何在 Azure 虛擬 WAN 中查看虛擬中樞的有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: a7e42ddeb4abacd8707dda4cd558933b0d7a34f4
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513701"
---
# <a name="view-virtual-hub-effective-routes"></a>檢視虛擬中樞的有效路由

您可以在 Azure 入口網站中查看虛擬 WAN 中樞的所有路由。 本文會逐步引導您查看有效路由的步驟。 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。

> [!NOTE]
> 在 Azure 入口網站上，其中有些功能可能仍在推出，而且在8月17日到一周之前都無法使用。 
>

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>選取連接或路由表

1. 流覽至您的虛擬中樞，然後選取 [**路由**]。 在 [路由] 頁面上，選取 [**有效路由**]。
1. 您可以從下拉式清單中選取 [**連線類型**] 或 [**路由表**]。 如果您沒有看到 [路由表] 選項，這表示您沒有在此虛擬中樞內設定的自訂或預設路由表。
1. 從 [連接] **/[路由表**] 的下拉式清單中，您可以從下列專案中選取：

   * 虛擬網路連接
   * VPN 網站連線
   * ExpressRoute 連線
   * 點對站連線
   * 路由表

   :::image type="content" source="./media/effective-routes-virtual-hub/routing.png" alt-text="路由":::

## <a name="view-output"></a><a name="output"></a>檢視輸出

頁面輸出會顯示下欄欄位：

* **前置**詞：目前實體已知的位址前置詞。
* **下一個躍點類型**：可以是虛擬網路連線、VPN_S2S_Gateway、ExpressRouteGateway、遠端中樞或 Azure 防火牆。
* **下一個躍點**：這是 IP，或只是顯示在連結上以隱含目前的中樞。
* **來源**：路由來源的資源識別碼。
* **As Path**： BGP Attribute as （自發系統）路徑列出的所有 AS 數位，都必須經過這些編號才能到達路徑所附加的前置詞，從公告。

### <a name="example"></a><a name="example"></a>範例

下列範例資料表中的值表示虛擬中樞連線或路由表已瞭解 10.2.0.0/24 （分支首碼）的路由。 因為**VPN 下一個躍點類型**VPN_S2S_Gateway VPN 閘道資源識別碼的**下一個躍**點，所以已瞭解路由。 **路由來源**指向來源 VPN 閘道/路由表/連接的資源識別碼。 **As path**表示分支的 as 路徑。

使用資料表底部的捲軸來查看 "AS Path"。

| **Prefix** |  **下一個躍點類型** | **下一個躍點** |  **路由來源** |**AS 路徑** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |10.1.0.6, 10.1.0.7|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**考量因素：**

* 如果您在**取得有效路由**輸出中看到 0.0.0.0/0，則表示路由存在於其中一個路由表中。 不過，如果已針對網際網路設定此路由，連接上就需要額外的旗標 **"enableInternetSecurity"： true** 。 如果連線上的 "enableInternetSecurity" 旗標為 "false"，則 VM NIC 上的有效路由不會顯示路由。

* 當您編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，會在 Azure 虛擬 WAN 入口網站中看到 [**傳播預設路由**] 欄位。 此欄位表示**enableInternetSecurity**旗標，其一律為 EXPRESSROUTE 和 VPN 連線的預設值為 "false"，但虛擬網路連線為 "true"。

## <a name="next-steps"></a>後續步驟

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱[關於虛擬中樞路由](about-virtual-hub-routing.md)。
