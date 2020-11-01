---
title: 將多個 VPN 閘道站對站連線新增至 VNet： Azure 入口網站
description: 將多個 S2S 連線新增至具有現有連線的 VPN 閘道
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/01/2020
ms.locfileid: "92890140"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>將其他 S2S 連線新增至 VNet： Azure 入口網站

> [!div class="op_single_selector"]
> * [Azure 入口網站](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (傳統)](vpn-gateway-multi-site.md)
>

本文可協助您將額外的站對站 (S2S) 連線新增至具有現有連線的 VPN 閘道。 此架構通常稱為「多網站」設定。 您可以將 S2S 連線新增到已經有 S2S 連線、點對站連線或 VNet 對 VNet 連線的 VNet。 新增連線有一些限制。 在開始設定之前，請先檢查本文中的 [必要條件](#before) 一節。

本文適用於具有路由型 VPN 閘道的 Resource Manager VNet。 這些步驟不適用於新的 ExpressRoute/站對站並存連線設定。 但是，如果您只是將新的 VPN 連線新增至現有的並存設定，則可以使用這些步驟。 如需有關並存連線的資訊，請參閱 [ExpressRoute/S2S 並存連線](../expressroute/expressroute-howto-coexist-resource-manager.md)。

## <a name="prerequisites"></a><a name="before"></a>必要條件

請確認下列事項：

* 您未設定新的並存 ExpressRoute 和 VPN 閘道設定。
* 您有使用具有現有連線之 Resource Manager 部署模型建立的虛擬網路。
* 您的 VNet 的虛擬網路閘道是路由型。 如果您的 VPN 閘道為原則型，您就必須刪除虛擬網路閘道，並建立新的路由型 VPN 閘道。
* 此 VNet 連接的所有 VNet 的位址範圍沒有重疊。
* 您有相容的 VPN 裝置以及能夠對其進行設定的人員。 請參閱 [關於 VPN 裝置](vpn-gateway-about-vpn-devices.md)。 如果不熟悉設定 VPN 裝置，或不熟悉位於內部部署網路組態的 IP 位址範圍，則您需要與能夠提供那些詳細資料的人協調。
* 您的 VPN 裝置有對外開放的公用 IP 位址。

## <a name="configure-a-connection"></a><a name="configure"></a>設定連接

1. 從瀏覽器流覽至 [Azure 入口網站](https://portal.azure.com) ，如有必要，請使用您的 Azure 帳戶登入。
1. 從資源清單中選取 [ **所有資源** ]，並找出您的 **虛擬網路閘道** ，然後選取它。
1. 在 [ **虛擬網路閘道** ] 頁面上，選取 [ **連接** ]。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="VPN 閘道連線":::
1. 在 [ **連接** ] 頁面上，選取 [ **+ 新增** ]。
1. 這會開啟 [ **加入連接** ] 頁面。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="VPN 閘道連線":::
1. 在 [新增連線]  頁面上，填寫下列欄位︰

   * **名稱︰** 為您要建立連線的網站提供名稱。
   * **連線類型：** 選取 [ **站對站] (IPsec)** 。

## <a name="add-a-local-network-gateway"></a><a name="local"></a>新增局域網路閘道

1. 在 [ **局域網路閘道** ] 欄位中，選取 **_[選擇局域網路閘道_ ] *。這會開啟 [* 選擇局域網路閘道** ] 頁面。
1. 選取 [ **+ 建立新** 的] 以開啟 [ **建立局域網路閘道** ] 頁面。

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="VPN 閘道連線":::
1. 在 [建立區域網路閘道]  頁面上，填寫下列欄位︰

   * **名稱：** 您要給區域網路閘道資源的名稱。
   * **端點：** 您要連接之網站上 VPN 裝置的公用 IP 位址，或端點的 FQDN。
   * **位址空間︰** 您要路由傳送至新的區域網路網站的位址空間。
1. 在 [ **建立局域網路閘道** ] 頁面上選取 **[確定]** 以儲存變更。

## <a name="add-the-shared-key"></a><a name="part3"></a>新增共用金鑰

1. 建立局域網路閘道之後，請返回 [ **新增連接** ] 頁面。
1. 完成其餘的欄位。 針對 **共用金鑰 (PSK)** ，您可以從 VPN 裝置取得共用金鑰，或在這裡進行設定，然後將 VPN 裝置設定為使用相同的共用金鑰。 重點是金鑰必須完全相同。

## <a name="create-the-connection"></a><a name="create"></a>建立連線

1. 在頁面底部，選取 **[確定]** 以建立連接。 連接會立即開始建立。
1. 連接完成後，您就可以加以查看並進行驗證。

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>查看並驗證 VPN 連線

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>下一步

一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱 [虛擬機器學習路徑](/learn/paths/deploy-a-website-with-azure-virtual-machines/)。
