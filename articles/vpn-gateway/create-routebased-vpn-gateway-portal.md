---
title: 建立以路由為基礎的 VPN 閘道：入口網站
titleSuffix: Azure VPN Gateway
description: 瞭解如何使用 Azure 入口網站建立以路由為基礎的 Azure VPN 閘道。 使用 VPN 閘道連接到您的內部部署網路。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: d78dff60c08a67305824139ba11f336380b0a018
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/07/2020
ms.locfileid: "87923369"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>使用 Azure 入口網站建立路由型 VPN 閘道

本文將協助您使用 Azure 入口網站，快速建立路由型 Azure VPN 閘道。  建立與內部部署網路的 VPN 連線時，會使用 VPN 閘道。 您也可以使用 VPN 閘道來連線至 VNet。 

本文中的步驟將會建立 VNet、子網路、閘道子網路，以及路由型 VPN 閘道 (虛擬網路閘道)。 完成閘道建立之後，您接著就可建立連線。 這些步驟需要 Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-virtual-network"></a><a name="vnet"></a>建立虛擬網路

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>設定並建立閘道

此步驟將帶您建立 VNet 的虛擬網路閘道。 建立閘道通常可能需要 45 分鐘或更久，視選取的閘道 SKU 而定。

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>基本閘道 SKU 不支援 IKEv2 或 RADIUS 驗證。 如果您打算讓 Mac 用戶端連線到您的虛擬網路，請勿使用基本 SKU。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>檢視 VPN 閘道

1. 建立閘道之後，瀏覽至入口網站中的 VNet1。 VPN 閘道會在 [概觀] 頁面上顯示為連線的裝置。

   ![連線的裝置](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "已連線的裝置")

2. 在裝置清單中，按一下 [VNet1GW]**** 以檢視詳細資訊。

   ![觀看 VPN 閘道](./media/create-routebased-vpn-gateway-portal/view-gateway.png "觀看 VPN 閘道")

## <a name="next-steps"></a>後續步驟

閘道建立完成之後，您便可在您的虛擬網路與另一個 VNet 之間建立連線。 或是在您的虛擬網路與內部部署位置之間建立連線。

> [!div class="nextstepaction"]
> [建立站對站連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [建立點對站連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [建立與另一個 VNet 的連線](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
