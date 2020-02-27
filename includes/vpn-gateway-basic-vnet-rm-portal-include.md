---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/25/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 872ba86c9e43b1f6642331908eb829605f6c19bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/26/2020
ms.locfileid: "77619779"
---
您可以使用下列步驟，透過 Resource Manager 部署模型和 Azure 入口網站建立 VNet。 如需虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>若要讓 VNet 連線到內部部署位置，請與內部部署網路系統管理員協調，以切割出此虛擬網路專用的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就會以未預期的方式路由傳送。 此外，如果您想要將此 VNet 連線到另一個 VNet，則位址空間不能與其他 VNet 重疊。 請據此規劃您的網路組態。
>
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 **[搜尋資源、服務和檔（G +/）** ] 中，輸入*虛擬網路*。

   ![找出虛擬網路資源頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "找出虛擬網路資源頁面")
1. 從**Marketplace**結果中選取 [**虛擬網路**]。

   ![選取虛擬網路](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "找出虛擬網路資源頁面")
1. 在 [**虛擬網路**] 頁面上，按一下 [**建立**]。

   ![[虛擬網路] 頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "按一下 [建立專案]。")
1. 按一下 [建立] 之後，[**建立虛擬網路**] 頁面隨即開啟。

   ![[建立虛擬網路] 頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/create-virtual-network-page.png "建立虛擬網路頁面")
1. 在 [建立虛擬網路] 頁面上進行 VNet 設定。 當您填寫欄位時，若欄位中輸入的字元經過驗證，紅色驚嘆號就會變成綠色核取記號。 系統會自動填寫某些值，您可使用自己的值加以取代：

   - **名稱**：輸入虛擬網路的名稱。
   - **位址空間**：輸入位址空間。 如果您有多個要新增的位址空間，請在此輸入您的第一個位址空間。 稍後，您可以在建立 VNet 之後新增其他位址空間。 如果您的設定需要 IPv6 位址空間，請核取此核取方塊以輸入該資訊。
   - **訂用帳戶**：確認列出的訂用帳戶是否正確。 您可以使用下拉式清單變更訂用帳戶。
   - **資源群組**：選取現有的資源群組，或輸入新資源群組的名稱，以建立一個新的群組。 如果您要建立新的群組，請根據您計劃的組態值來命名資源群組。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/management/overview.md#resource-groups)。
   - **位置**：選取 VNet 的位置。 此位置會決定您部署到此 VNet 的資源存留的位置。
   - **子網**：新增子網**名稱**和子網**位址範圍**。 稍後，您可以在建立 VNet 之後新增其他子網路。
   - **DDos 保護**：除非您想要使用標準服務，否則請選取 [**基本**]。
   - **服務端點**：除非您的設定指定此設定，否則您可以將此設定保留為**停用**。
   - **防火牆**：除非您的設定指定此設定，否則您可以將此設定保留為**停用**。
1. 按一下 [**建立**] 以開始虛擬網路部署。
