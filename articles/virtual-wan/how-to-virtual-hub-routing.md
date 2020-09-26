---
title: 如何設定虛擬中樞路由
titleSuffix: Azure Virtual WAN
description: 本文說明如何設定虛擬中樞路由
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: cae74a5f4859d208765c9a7e5cde05ff3c0c3096
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91313683"
---
# <a name="how-to-configure-virtual-hub-routing"></a>如何設定虛擬中樞路由

虛擬中樞可以包含多個閘道，例如站對站 VPN 閘道、ExpressRoute 閘道、點對站閘道和 Azure 防火牆。 虛擬中樞內的路由功能是由使用邊界閘道協定 (BGP) 的閘道管理所有路由的路由器（包括傳輸路由）所提供。 此路由器也會在連線至虛擬中樞的虛擬網路之間提供傳輸連線能力，且最多可支援 50 Gbps 的匯總輸送量。 這些路由功能適用于標準虛擬 WAN 客戶。

如需詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。

## <a name="create-a-route-table"></a><a name="create-table"></a>建立路由表

1. 在 Azure 入口網站中，流覽至虛擬中樞。
2. 在 [連線 **能力**] 底下，選取 [ **路由**]。 在 [路由] 頁面上，您會看到 **預設值** 和 **無** 路由表。

   :::image type="content" source="./media/how-to-virtual-hub-routing/routing.png" alt-text="路由頁面":::
3. 選取 [ **+ 建立路由表** ] 以開啟 [ **建立路由表** ] 頁面。
4. 在 [建立路由表] 頁面的 [ **基本** ] 索引標籤上，完成下欄欄位。

   :::image type="content" source="./media/how-to-virtual-hub-routing/basics.png" alt-text="[基本] 索引標籤":::

   * **Name**
   * **路由**
   * **路由名稱**
   * **目的地類型**
   * **目的地首碼**：您可以匯總前置詞。 例如： VNet 1： 10.1.0.0/24 和 VNet 2： 10.1.1.0/24 可以匯總為 10.1.0.0/16。 **分支** 路由適用于所有連線的 VPN 網站、ExpressRoute 線路和使用者 VPN 連線。
   * **下一個躍點**：虛擬網路連線或 Azure 防火牆的清單。

     如果您選取虛擬網路連線，您會看到 **設定靜態路由**。 這是選擇性的設定。 如需詳細資訊，請參閱設定 [靜態路由](about-virtual-hub-routing.md#static)。

      :::image type="content" source="./media/how-to-virtual-hub-routing/next-hop.png" alt-text="下一個躍點":::

5. 選取 [ **標籤** ] 索引標籤以設定標籤名稱。 標籤提供以邏輯方式分組路由表的機制。

    :::image type="content" source="./media/how-to-virtual-hub-routing/labels.png" alt-text="設定標籤名稱":::

6. 選取 [ **關聯** ] 索引標籤，將連接關聯至路由表。
您會看到 **分支**、 **虛擬網路**和連線的 **目前設定** 。

    :::image type="content" source="./media/how-to-virtual-hub-routing/associations.png" alt-text="路由表的關聯連接":::

7. 選取 [ **傳播** ] 索引標籤，將路由從連接傳播至路由表。

    :::image type="content" source="./media/how-to-virtual-hub-routing/propagations.png" alt-text="傳播路由":::

8. 選取 [ **建立** ] 以建立路由表。

## <a name="to-edit-a-route-table"></a><a name="edit-table"></a>編輯路由表

在 Azure 入口網站中，找出您虛擬中樞的路由表。 選取路由表以編輯任何資訊。

## <a name="to-delete-a-route-table"></a><a name="delete-table"></a>刪除路由表

在 Azure 入口網站中，找出您虛擬中樞的路由表。 您無法刪除預設值或無路由表。 不過，您可以刪除所有自訂路由表。 按一下 **[...]**，然後選取 [ **刪除**]。

## <a name="to-view-effective-routes"></a><a name="view-routes"></a>若要查看有效的路由

在 Azure 入口網站中，找出您虛擬中樞的路由表。 按一下 **[...]** 並選取 [ **有效路由** ]，以查看所選路由表所學習到的路由。 從連接到路由表的傳播路由，會自動填入路由表的 **有效路由** 中。 如需詳細資訊，請參閱 [關於有效路由](effective-routes-virtual-hub.md)。

:::image type="content" source="./media/how-to-virtual-hub-routing/effective.png" alt-text="查看有效路由" lightbox="./media/how-to-virtual-hub-routing/effective-expand.png":::

## <a name="to-set-up-routing-configuration-for-a-virtual-network-connection"></a><a name="routing-configuration"></a>設定虛擬網路連接的路由設定

1. 在 Azure 入口網站中，流覽至您的虛擬 WAN，並在 [連線 **能力**] 底下選取 [ **虛擬網路**連線]。
1. 選取 [ **+ 新增連接**]。
1. 從下拉式清單中選取虛擬網路。
1. 設定要與路由表建立關聯的路由設定。 在 [ **關聯路由表**] 中，從下拉式清單中選取路由表。
1. 設定路由設定，以傳播至一或多個路由表。 在 [ **傳播至路由表**] 中，從下拉式清單中選取。
1. 針對 **靜態路由**，請設定網路虛擬裝置 (的靜態路由（如果適用) ）。 虛擬 WAN 針對虛擬網路連線中的靜態路由支援單一下一個躍點 IP。 例如，如果您有不同的虛擬裝置用於輸入和輸出流量，則最好讓虛擬裝置在不同的 Vnet 中，並將 Vnet 附加至虛擬中樞。


:::image type="content" source="./media/how-to-virtual-hub-routing/routing-configuration.png" alt-text="設定路由設定" lightbox="./media/how-to-virtual-hub-routing/routing-configuration-expand.png":::

## <a name="next-steps"></a>後續步驟

如需虛擬中樞路由的詳細資訊，請參閱 [關於虛擬中樞路由](about-virtual-hub-routing.md)。
如需虛擬 WAN 的詳細資訊，請參閱[常見問題集](virtual-wan-faq.md)。
