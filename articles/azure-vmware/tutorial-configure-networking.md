---
title: 教學課程 - 在 Azure 中設定 VMware 私人雲端的網路功能
description: 了解如何建立及設定在 Azure 中部署私人雲端所需的網路功能
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 5a8086f78f465f52d0f9107932c09c4690f505e8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873857"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>教學課程：在 Azure 中設定 VMWare 私人雲端的網路功能

Azure VMware 解決方案 (AVS) 私人雲端需要虛擬網路。 因為 AVS 不會在預覽期間支援您的內部部署 vCenter，所以需要與內部部署環境整合的其他步驟。 您也需要設定 ExpressRoute 線路和虛擬網路閘道，我們也將在本教學課程中加以說明。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路閘道
> * 將您的 ExpressRoute 線路連線至閘道
> * 找出 vCenter 和 NSX 管理員的 URL

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。

## <a name="create-a-virtual-network"></a>建立虛擬網路

瀏覽至您在 [上一個教學課程](tutorial-create-private-cloud.md)中建立的資源群組，然後選取 [+新增] 以定義新的資源。

在 [搜尋 Marketplace] 文字方塊中，輸入**虛擬網路**。 尋找虛擬網路資源並選取。

在虛擬網路頁面上，選取 [建立]，為您的私人雲端設定虛擬網路。

在 [建立虛擬網路] 頁面上，輸入虛擬網路的相關詳細資料，各項屬性的描述如下表所示：

> [!IMPORTANT]
> 位址空間**不得**與您在上一個教學課程中建立私人雲端時所使用的位址空間重疊。

在 基本概念 索引標籤上，輸入虛擬網路的名稱並選取適當的區域，然後選取 下一步 **：** IP 位址

在 [IP 位址] 索引標籤的 **IPv4 位址空間**下，輸入您在上一個教學課程中建立的位址空間。

選取 [+新增子網路]，然後在 [新增子網路] 頁面上，為子網路提供名稱和適當的位址範圍。 完成時選取 [新增]。

選取 [檢閱+建立]

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="建立虛擬網路" border="true":::

確認資訊並選取 [建立]。 部署完成之後，您會在資源群組中看到您的虛擬網路。

## <a name="create-a-virtual-network-gateway"></a>建立虛擬網路閘道

您已在上一節中建立虛擬網路，現在將建立虛擬網路閘道。

在資源群組中，選取 [+新增] 以新增新的資源。

在 [搜尋 Marketplace] 文字方塊中，輸入**虛擬網路閘道**。 尋找虛擬網路資源並選取。

在 [虛擬網路閘道] 頁面上，選取 [建立]。

在 [建立虛擬網路閘道] 頁面的基本索引標籤上，提供欄位的值。 下表將顯示各個欄位的說明：

| 欄位 | 值 |
| --- | --- |
| **訂用帳戶** | 此值已填入資源群組所屬的訂用帳戶。 |
| **資源群組** | 已為目前的資源群組填入此值。 這應該是您在上一個測試中建立的資源群組。 |
| **名稱** | 輸入虛擬網路閘道的唯一名稱。 |
| **區域** | 選取虛擬網路閘道的地理位置。 |
| **閘道類型** | 選取 [ExpressRoute]。 |
| **VPN 類型** | 選取 [依路由]。 |
| **SKU** | 將預設值保持為：**標準**。 |
| **虛擬網路** | 選取您先前建立的虛擬網路。 如果看不到虛擬網路，請確定閘道的區域符合虛擬網路的區域。 |
| **閘道子網路位址範圍** | 選取虛擬網路時，就會填入此值。 請勿變更預設值。 |
| **公用 IP 位址** | 選取 [建立新的]。 |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="建立閘道" border="true":::

選取 [檢閱+建立]，在下一個頁面上確認詳細資料是否正確，然後選取 [建立] 以開始部署虛擬網路閘道。 部署完成之後，請移至本教學課程的下一節，將您的 ExpressRoute 連線連線到包含私人雲端的虛擬網路。

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>將 ExpressRoute 連線到虛擬網路閘道

本節會逐步引導您新增 AVS 私人雲端與您所建立的虛擬網路閘道間的連線。

瀏覽至您在上一個教學課程中建立的私ㄟ雲端，然後選取 [管理] 下的 [連線]，選取 [ExpressRoute] 索引標籤。

複製授權金鑰。 如果沒有授權金鑰，您需要建立一個，然後選取 [+要求授權金鑰]

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="要求授權金鑰" border="true":::

瀏覽至您在上一個步驟中建立的虛擬網路閘道，然後在 [設定]下，選取 [連線]。 在 [連線] 頁面上，選取 [+新增]。

在 [新增連線] 頁面上，提供欄位的值。 下表將顯示各個欄位的說明：

| 欄位 | 值 |
| --- | --- |
| **名稱**  | 輸入連線的名稱。  |
| **連線類型**  | 選取 [ExpressRoute]。  |
| **兌換授權**  | 請務必選取此方塊。  |
| **虛擬網路閘道** | 您先前建立的虛擬網路閘道  |
| **授權金鑰**  | 複製並貼上您資源群組的 ExpressRoute 索引標籤中的授權金鑰。 |
| **對等線路 URI**  | 複製並貼上您資源群組的 ExpressRoute 索引標籤中的 ExpressRoute 識別碼。  |

選取 [確定]。 這會在您的 ExpressRoute 線路與虛擬網路之間建立連線。

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="新增連線" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>找出 vCenter 和 NSX 管理員的 URL

若要登入 vVenter 和 NSX 管理員，您需要 vCenter Web 用戶端的 URL 和 NSX-T 管理員網站。 若要尋找 URL：

瀏覽至您的 AVS 私人雲端，在 [管理] 下選取 [身分識別]，您可以在這裡找到所需的資訊。

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="找出 vCenter URL" border="true":::

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已了解如何：

> [!div class="checklist"]
> * 建立虛擬網路
> * 建立虛擬網路閘道
> * 將您的 ExpressRoute 線路連線至閘道
> * 找出 vCenter 和 NSX 管理員的 URL

繼續進行下一個教學課程，以了解如何建立用來連線到您環境的 jumpbox，讓您可以在本機管理私人雲端。

> [!div class="nextstepaction"]
> [存取私人雲端](tutorial-access-private-cloud.md)
