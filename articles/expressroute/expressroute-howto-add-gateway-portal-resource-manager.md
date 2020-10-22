---
title: 教學課程：Azure ExpressRoute - 將閘道新增至 VNet (Azure 入口網站)
description: 本教學課程將逐步引導您使用 Azure 入口網站，將虛擬網路閘道新增至 ExpressRoute 的 VNet。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "91761771"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>教學課程：使用 Azure 入口網站為 ExpressRoute 設定虛擬網路閘道
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [傳統 - PowerShell](expressroute-howto-add-gateway-classic.md)
> * [影片 - Azure 入口網站](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

本教學課程將逐步引導您完成為既存的 VNet 新增虛擬網路閘道的步驟。 本文將逐步引導您完成為既存的 VNet 新增虛擬網路 (VNet) 閘道、調整該閘道大小及移除該閘道的步驟。 此組態的步驟是使用 Resource Manager 部署模型來建立的 VNet 所專用，而在 ExpressRoute 組態中也將使用該部署模型。 如需有關 ExpressRoute 之虛擬網路閘道和閘道組態設定的詳細資訊，請參閱[關於 ExpressRoute 的虛擬網路閘道](expressroute-about-virtual-network-gateways.md)。 

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> - 建立閘道子網路。
> - 建立虛擬網路閘道。

## <a name="prerequisites"></a>必要條件

此工作的步驟會根據下列組態參考清單中的值來使用 VNet。 我們會在範例步驟中使用此清單。 您可以複製清單以供參考，並使用您自己的值來取代其中的值。

**組態參考清單**

* 虛擬網路名稱 = "TestVNet"
* 虛擬網路位址空間 = 192.168.0.0/16
* 子網路名稱 = "FrontEnd" 
    * 子網路位址空間 = "192.168.1.0/24"
* 資源群組 = "TestRG"
* 位置 = "美國東部"
* 閘道器子網路名稱："GatewaySubnet"，您必須一律將閘道器子網路命名為 *GatewaySubnet*。
    * 閘道子網路位址空間 = "192.168.200.0/26"
* 閘道名稱 = "ERGW"
* 閘道公用 IP 名稱 = "MyERGWVIP"
* 閘道類型 = "ExpressRoute"，ExpressRoute 組態需要這個類型。

您可以先檢視這些步驟的[影片](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)，再開始進行設定。

## <a name="create-the-gateway-subnet"></a>建立閘道子網路

1. 在[入口網站](https://portal.azure.com)中，瀏覽至要建立虛擬網路閘道的 Resource Manager 虛擬網路。
1. 在 VNet 的 [設定] 區段中，選取 [子網路] 以展開 [子網路] 設定。
1. 在 [子網路] 設定中，選取 [+ 閘道子網路] 以新增閘道子網路。 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="新增閘道子網路":::

1. 子網路的 [名稱]**** 會自動填入 'GatewaySubnet' 這個值。 為了讓 Azure 將此子網路視為閘道子網路，需要有這個值。 調整自動填入的 [位址範圍] 值，以符合您的組態需求。 建議您以 /27 或更大的值 (/26、/25 等) 建立閘道子網路。 然後，選取 [確定] 來儲存值並建立閘道子網路。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="新增閘道子網路":::

## <a name="create-the-virtual-network-gateway"></a>建立虛擬網路閘道

1. 在入口網站中的左側選取 [建立資源]，並在搜尋中輸入「虛擬網路閘道」。 在搜尋傳回的結果中找出**虛擬網路閘道**，然後選取該項目。 在 [虛擬網路閘道] 頁面上，選取 [建立]。
1. 在 [建立虛擬網路閘道] 頁面上，輸入或選取下列設定：

    | 設定 | 值 |
    | --------| ----- |
    | 訂用帳戶 | 請確認已選取正確的訂用帳戶。 |
    | 資源群組 | 一旦您選取虛擬網路，就會自動選擇資源群組。 | 
    | 名稱 | 為您的閘道命名。 這與為閘道子網路命名不同。 這是您要建立之閘道物件的名稱。|
    | 區域 | 變更 [區域] 欄位以指向您的虛擬網路所在的位置。 如果位置並未指向您的虛擬網路所在的區域，則此虛擬網路不會出現在 [選擇虛擬網路] 下拉式清單中。 |
    | 閘道類型 | 選取 [ExpressRoute]|
    | SKU | 從下拉式清單中選取閘道 SKU。 |
    | 虛擬網路 | 選取 [TestVNet]。 |
    | 公用 IP 位址 | 選取 [建立新的]****。|
    | 公用 IP 位址名稱 | 提供公用 IP 位址的名稱。 |

1. 選取 [檢閱+建立]，然後選取 [建立] 以開始建立閘道。 系統會驗證設定並部署閘道。 建立虛擬網路閘道最多可能需要花費 45 分鐘的時間來完成。

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="新增閘道子網路":::

## <a name="clean-up-resources"></a>清除資源

如果您不再需要 ExpressRoute 閘道，請在虛擬網路資源群組中找出閘道，然後選取 [刪除]。 請確定閘道沒有與任何線路連線。

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="新增閘道子網路":::

## <a name="next-steps"></a>後續步驟
建立 VNet 閘道之後，您可以將 VNet 連結至 ExpressRoute 循環。 

> [!div class="nextstepaction"]
> [將虛擬網路連結至 ExpressRoute 線路](expressroute-howto-linkvnet-portal-resource-manager.md)
