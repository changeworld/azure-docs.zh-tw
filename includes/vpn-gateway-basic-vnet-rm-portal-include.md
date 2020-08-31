---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 517acc5137d70c722d8defade1e218a3b2e78f86
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89052451"
---
您可以使用下列步驟，透過 Resource Manager 部署模型和 Azure 入口網站建立 VNet。 如需虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>在使用虛擬網路作為用作跨單位結構的一部分時，請確保與內部部署網路系統管理員協調，以切割出此虛擬網路專用的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就會以未預期的方式路由傳送。 此外，如果要將此虛擬網路連線至另一個虛擬網路，則位址空間不能與其他虛擬網路重疊。 請據此規劃您的網路組態。
>
>

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 在 [搜尋資源、服務和文件 (G+/)] 中，輸入 [虛擬網路]。

   ![尋找虛擬網路資源頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "尋找虛擬網路資源頁面")
1. 從 **Marketplace** 結果中選取 [虛擬網路]。

   ![選取虛擬網路](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "尋找虛擬網路資源頁面")
1. 在 [虛擬網路] 頁面上，選取 [建立]。

   ![虛擬網路頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "選取 [建立]")
1. 當您選取 [建立] 之後，[建立虛擬網路] 頁面隨即開啟。
1. 在 [基本] 索引標籤上，設定 [專案詳細資料] 和 VNet 設定的 [執行個體詳細資料]。

   ![基本索引標籤](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "[基本] 索引標籤")當您填寫欄位時，若欄位中輸入的字元經過驗證，就會變成綠色核取記號。 系統會自動填寫某些值，您可使用自己的值加以取代：

   - **訂用帳戶**：確認列出的訂用帳戶是否正確。 您可以使用下拉式清單變更訂用帳戶。
   - **資源群組**：選取現有的資源群組，或按一下 [新建] 來建立新群組。 如需有關資源群組的詳細資訊，請參閱 [Azure Resource Manager 概觀](../articles/azure-resource-manager/management/overview.md#resource-groups)。
   - **Name**：輸入虛擬網路的名稱。
   - **區域**：選取您的 VNet 位置。 此位置會決定您部署到此 VNet 的資源存留的位置。

1. 在 [IP 位址] 索引標籤上，設定值。 下列範例中所顯示的值是供示範之用。 根據您所需的設定來調整這些值。

   ![IP 位址索引標籤](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP 位址索引標籤")  
   - **IPv4 位址空間**：依預設，系統會自動建立位址空間。 您可以按一下位址空間來加以調整，以反映自己的值， 也可以新增其他位址空間。
   - **子網路**：如果您使用預設的位址空間，則系統會自動建立預設子網路。 如果您變更位址空間，則需要新增子網路。 選取 [+ 新增子網路] 以開啟 [新增子網路] 視窗。 設定下列設定，然後選取 [新增] 以新增值：
      - **子網路名稱**：在此範例中，我們將子網路命名為「FrontEnd」。
      - **子網路位址範圍**︰此子網路的位址範圍。

1. 在 [安全性] 索引標籤上，目前先保留預設值：

   - **DDoS 保護**：基本
   - **防火牆**：已停用
1. 選取 [檢閱 + 建立] 來驗證虛擬網路設定。
1. 在驗證設定之後，請選取 [建立]。
