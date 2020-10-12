---
title: 查看虛擬中樞的有效路由： Azure 虛擬 WAN |Microsoft Docs
description: 如何在 Azure 虛擬 WAN 中查看虛擬中樞的有效路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: ad487aa7492abcf02988c78ccfa2ba7cd4798249
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983646"
---
# <a name="view-virtual-hub-effective-routes"></a>檢視虛擬中樞的有效路由

您可以在 Azure 入口網站中，查看虛擬 WAN 中樞的所有路由。 本文將逐步引導您完成查看有效路由的步驟。 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="select-connections-or-route-tables"></a><a name="routing"></a>選取連接或路由表

1. 流覽至您的虛擬中樞，然後選取 [ **路由**]。 在 [路由] 頁面上，選取 [ **有效路由**]。
1. 您可以從下拉式清單中選取 [ **路由表**]。 如果您沒有看到路由表選項，這表示您沒有在此虛擬中樞內設定的自訂或預設路由表。

## <a name="view-output"></a><a name="output"></a>檢視輸出

頁面輸出會顯示下欄欄位：

* **首碼**： (從虛擬中樞路由器學會的目前實體位址首碼) 
* **下一個躍點類型**：可以是虛擬網路連線、VPN_S2S_Gateway、ExpressRouteGateway、遠端中樞或 Azure 防火牆。
* **下一個躍點**：此為下一個躍點之資源識別碼的連結，或只會顯示連結以表示目前的中樞。
* **來源**：路由來源的資源識別碼。
* **As Path**： BGP 屬性 as (自發系統) 路徑會列出所有 AS 號碼，而這些數位必須經過傳遞，才能到達路徑所附加的首碼位置，而是從公告。

### <a name="example"></a><a name="example"></a>範例

下列範例表中的值表示虛擬中樞連接或路由表已瞭解 10.2.0.0/24 的路由， (分支首碼) 。 因為 **VPN 下一個躍點類型** VPN_S2S_Gateway 具有 **下一個躍點** VPN 閘道資源識別碼，所以已瞭解路由。 **路由來源** 指向原始 VPN 閘道/路由表/連接的資源識別碼。 **As 路徑** 表示分支的 as 路徑。

使用資料表底部的捲軸來查看「AS 路徑」。

| **Prefix** |  **下一個躍點類型** | **下一個躍點** |  **路由來源** |**AS 路徑** |
| ---        | ---                | ---          | ---               | ---         |
| 10.2.0.0/24| VPN_S2S_Gateway |/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw|/subscriptions/ `<sub id>` /ResourceGroups/ `<resource group name>` /providers/Microsoft.Network/vpnGateways/vpngw| 20000|

**考量因素：**

* 如果您在「 **取得有效路由** 」輸出中看到 0.0.0.0/0，表示路由存在於其中一個路由表中。 但是，如果此路由是針對網際網路設定的，則連接上需要有額外的旗標 **"了 enableinternetsecurity"： true** 。 如果連接上的 "了 enableinternetsecurity" 旗標為 "false"，則 VM NIC 上的有效路由不會顯示路由。

* 當您編輯虛擬網路連線、VPN 連線或 ExpressRoute 連線時，會在 Azure 虛擬 WAN 入口網站中看到 [ **傳播預設路由** ] 欄位。 此欄位表示 **了 enableinternetsecurity** 旗標，此旗標一律預設為 "false" （適用于 EXPRESSROUTE 和 VPN 連線），但針對虛擬網路連線，則為 "true"。

* 查看 VM NIC 上的有效路由時，如果您看到下一個躍點為「虛擬網路閘道」，則當 VM 位於連接到虛擬 WAN 中樞的輪輻中時，這表示虛擬中樞路由器。

* 只有當虛擬中樞至少有一種連線 (VPN/ER/VNET) 與其連線時，才會填入虛擬中樞路由表的有效路由。

## <a name="next-steps"></a>後續步驟

* 如需有關虛擬 WAN 的詳細資訊，請參閱[虛擬 WAN 概觀](virtual-wan-about.md)。
* 如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
