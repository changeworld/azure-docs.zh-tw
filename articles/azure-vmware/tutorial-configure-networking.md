---
title: 教學課程 - 在 Azure 中設定 VMware 私人雲端的網路功能
description: 了解如何建立及設定在 Azure 中部署私人雲端所需的網路功能
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: 6aff39284f3ea786080055552ac001ac5dd7b394
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/30/2020
ms.locfileid: "91578340"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>教學課程：在 Azure 中設定 VMWare 私人雲端的網路功能

Azure VMware 解決方案私人雲端需要 Azure 虛擬網路。 由於 Azure VMware 解決方案不支援您的內部部署 vCenter，因此需執行額外的步驟與內部部署環境整合。 此外也必須設定 ExpressRoute 線路和虛擬網路閘道。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路閘道
> * 將您的 ExpressRoute 線路連線至閘道
> * 找出 vCenter 和 NSX 管理員的 URL

## <a name="prerequisites"></a>必要條件 
您建立了 [Azure VMware 解決方案私人雲端](tutorial-create-private-cloud.md)的虛擬網路。 

## <a name="create-a-virtual-network"></a>建立虛擬網路

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 瀏覽至您在[建立私人雲端教學課程](tutorial-create-private-cloud.md)中建立的資源群組，然後選取 [+新增] 以定義新的資源。 

1. 在 [搜尋 Marketplace] 文字方塊中，輸入**虛擬網路**。 尋找虛擬網路資源並選取。

1. 在 [虛擬網路] 頁面上，選取 [建立]，為您的私人雲端設定虛擬網路。

1. 在 [建立虛擬網路] 頁面上，輸入虛擬網路的詳細資料。

1. 在 [基本概念] 索引標籤上，輸入虛擬網路的名稱並選取適當的區域，然後選取 [下一步 **：** IP 位址]。

1. 在 [IP 位址] 索引標籤的 **IPv4 位址空間**下，輸入您在上一個教學課程中建立的位址空間。

   > [!IMPORTANT]
   > 位址空間**不得**與您在上一個教學課程中建立私人雲端時所使用的位址空間重疊。

1. 選取 [+新增子網路]，然後在 [新增子網路] 頁面上，為子網路提供名稱和適當的位址範圍。 完成時選取 [新增]。

1. 選取 [檢閱 + 建立]。

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="選取 [檢閱 + 建立]。" border="true":::

1. 確認資訊並選取 [建立]。 部署完成之後，您會在資源群組中看到您的虛擬網路。

## <a name="create-a-virtual-network-gateway"></a>建立虛擬網路閘道

既然您已建立虛擬網路，就會建立虛擬網路閘道。

1. 在資源群組中，選取 [+新增] 以新增新的資源。

1. 在 [搜尋 Marketplace] 文字方塊中，輸入**虛擬網路閘道**。 尋找虛擬網路資源並選取。

1. 在 [虛擬網路閘道] 頁面上，選取 [建立]。

1. 在 [建立虛擬網路閘道] 頁面的 [基本資料] 索引標籤上，提供欄位的值，然後選取 [檢閱 + 建立]。 

   | 欄位 | 值 |
   | --- | --- |
   | **訂用帳戶** | 預先填入的值，包含資源群組所屬的訂用帳戶。 |
   | **資源群組** | 為目前的資源群組預先填入的值。 值應為您在先前的測試中建立的資源群組。 |
   | **名稱** | 輸入虛擬網路閘道的唯一名稱。 |
   | **區域** | 選取虛擬網路閘道的地理位置。 |
   | **閘道類型** | 選取 [ExpressRoute]。 |
   | **SKU** | 將預設值保持為：**標準**。 |
   | **虛擬網路** | 選取您先前建立的虛擬網路。 如果看不到虛擬網路，請確定閘道的區域符合虛擬網路的區域。 |
   | **閘道子網路位址範圍** | 選取虛擬網路時，就會填入此值。 請勿變更預設值。 |
   | **公用 IP 位址** | 選取 [建立新的]。 |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="選取 [檢閱 + 建立]。" border="true":::

1. 驗證詳細資料是否正確，然後選取 [建立] 以開始部署虛擬網路閘道。 
1. 部署完成之後，請移至下一節，將您的 ExpressRoute 連線到包含 Azure VMware 解決方案私人雲端的虛擬網路。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>將 ExpressRoute 連線到虛擬網路閘道

既然您已部署虛擬網路閘道，就會在其與您的 Azure VMware 解決方案私人雲端之間新增連線。

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>找出 vCenter 和 NSX 管理員的 URL

若要登入 vCenter 和 NSX 管理員，您需要 vCenter Web 用戶端的 URL 和 NSX-T 管理員網站。 

瀏覽至您的 Azure VMware 解決方案私人雲端，在 [管理] 下選取 [身分識別]，您可以在這裡找到所需的資訊。

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="選取 [檢閱 + 建立]。" border="true":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路閘道
> * 將您的 ExpressRoute 線路連線至閘道
> * 找出 vCenter 和 NSX 管理員的 URL

繼續進行下一個教學課程，了解如何建立 vCenter 中的 VM 所使用的 NSX-T 網路區段。

> [!div class="nextstepaction"]
> [建立 NSX-T 網路區段](tutorial-nsx-t-network-segment.md)