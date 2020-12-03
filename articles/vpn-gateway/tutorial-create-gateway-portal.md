---
title: 教學課程 - 建立和管理 VPN 閘道：Azure 入口網站
description: 遵循本教學課程，以了解如何使用入口網站來建立、部署和管理 Azure VPN 閘道
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: e2df34b7897ee2543d967d147317859ab26971e9
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510469"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>教學課程：使用 Azure 入口網站建立和管理 VPN 閘道

Azure VPN 閘道提供客戶組織內部與 Azure 之間的跨單位連線。 本教學課程將說明基本的 Azure VPN 閘道部署項目，例如建立和管理 VPN 閘道。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立 VPN 閘道
> * 檢視閘道公用 IP 位址
> * 調整 VPN 閘道的大小 (調整 SKU 的大小)
> * 重設 VPN 閘道

下圖顯示在本教學課程中建立的虛擬網路和 VPN 閘道。

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="VNet 和 VPN 閘道圖表":::

## <a name="prerequisites"></a>必要條件

具有有效訂用帳戶的 Azure 帳戶。 如果您沒有帳戶，請[免費建立一個](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>建立虛擬網路

使用下列值建立 VNet：

* **資源群組：** TestRG1
* **名稱：** VNet1
* **區域：** (美國) 美國東部
* **IPv4 位址空間：** 10.1.0.0/16
* **子網路名稱：** FrontEnd
* **子網路位址空間：** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>建立 VPN 閘道

此步驟將帶您建立 VNet 的虛擬網路閘道。 建立閘道通常可能需要 45 分鐘或更久，視選取的閘道 SKU 而定。

使用下列值建立虛擬網路閘道：

* **名稱：** VNet1GW
* **區域：** 美國東部
* **閘道類型：** VPN
* **VPN 類型：** 以路由為基礎
* **SKU：** VpnGw1
* **世代：** Generation1
* **虛擬網路：** VNet1
* **閘道子網路位址範圍：** 10.1.255.0/27
* **公用 IP 位址：** 新建
* **公用 IP 位址名稱：** VNet1GWpip
* **啟用主動-主動模式：** 已停用
* **設定 BGP：** 已停用

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-public-ip-address"></a><a name="view"></a>檢視公用 IP 位址

您可以在閘道的 [概觀] 頁面上，檢視閘道的公用 IP 位址。

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="概觀分頁":::

若要查看公用 IP 位址物件的其他資訊，請按一下 [公用 IP 位址] 旁的 [名稱/IP 位址] 連結。

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>調整閘道 SKU 的大小

閘道 SKU 的大小調整與變更有相關特定規則。 在本節中，我們會調整 SKU 的大小。 如需詳細資訊，請參閱[閘道設定 - 調整大小和變更 SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange)。

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>重設閘道

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續使用此應用程式或移至下一個教學課程，請使用下列步驟刪除這些資源：

1. 在入口網站頂端的 [搜尋] 方塊中輸入您的資源群組，然後從搜尋結果中進行選取。

1. 選取 [刪除資源群組]。

1. 針對 [輸入資源群組名稱] 輸入您的資源群組，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

擁有 VPN 閘道之後，您就可以設定連線。 下列文章將協助您建立幾個最常見的設定：

> [!div class="nextstepaction"]
> [站對站 VPN 連線](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

> [!div class="nextstepaction"]
> [點對站 VPN 連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
