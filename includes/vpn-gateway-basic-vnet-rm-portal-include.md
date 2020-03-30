---
title: 包含檔案
description: 包含檔案
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/03/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d2cf1a2e2ab9cf2d6e35aa12b5b0f8ddc04ad0e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78301929"
---
您可以使用下列步驟，透過 Resource Manager 部署模型和 Azure 入口網站建立 VNet。 如需虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。

>[!NOTE]
>將虛擬網路用作跨界體系結構的一部分時，請確保與本地網路系統管理員協調以雕刻可用於此虛擬網路的 IP 位址範圍。 如果 VPN 連線的兩端存在重複的位址範圍，流量就會以未預期的方式路由傳送。 此外，如果要將此虛擬網路連接到另一個虛擬網路，位址空間不能與其他虛擬網路重疊。 請據此規劃您的網路組態。
>
>

1. 登錄到 Azure[門戶](https://portal.azure.com)。
1. 在**搜索資源、服務和文檔 （G+/） 中**，鍵入*虛擬網路*。

   ![查找虛擬網路資源頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace.png "查找虛擬網路資源頁面")
1. 從**應用商店**結果中選擇**虛擬網路**。

   ![選擇虛擬網路](./media/vpn-gateway-basic-vnet-rm-portal-include/marketplace-results.png "查找虛擬網路資源頁面")
1. 在**虛擬網路**頁上，選擇 **"創建**"。

   ![虛擬網路頁面](./media/vpn-gateway-basic-vnet-rm-portal-include/vnet-click-create.png "選取 [建立]")
1. 選擇 **"創建**"後，將打開 **"創建虛擬網路**"頁。
1. 在 **"基本"** 選項卡上，配置**專案詳細資訊**和**實例詳細資訊**VNet 設置。

   ![基礎知識選項卡](./media/vpn-gateway-basic-vnet-rm-portal-include/basics.png "[基本] 索引標籤")填寫欄位時，在驗證欄位中輸入的字元時，會看到綠色核取記號。 系統會自動填寫某些值，您可使用自己的值加以取代：

   - **訂用帳戶**：確認列出的訂用帳戶是否正確。 您可以使用下拉式清單變更訂用帳戶。
   - **資源組**：選擇現有資源組，或按一下"**創建新**資源組"以創建新資源組。 有關資源組的詳細資訊，請參閱[Azure 資源管理器概述](../articles/azure-resource-manager/management/overview.md#resource-groups)。
   - **名稱**：輸入虛擬網路的名稱。
   - **區域**：選擇 VNet 的位置。 此位置會決定您部署到此 VNet 的資源存留的位置。

1. 在 **"IP 位址"** 選項卡上，配置值。 以下示例中顯示的值用於演示目的。 根據所需的設置調整這些值。

   ![IP 位址選項卡](./media/vpn-gateway-basic-vnet-rm-portal-include/addresses.png "IP 位址選項卡")  
   - **IPv4 位址空間**：預設情況下，將自動創建位址空間。 您可以按一下位址空間來調整它以反映您自己的值。 您還可以添加其他位址空間。
   - **IPv6**：如果您的配置需要 IPv6 位址空間，請選擇 **"添加 IPv6 位址空間**"框以輸入該資訊。
   - **子網**：如果使用預設位址空間，將自動創建預設子網。 如果更改位址空間，則需要添加子網。 選擇 **= 添加子網**以打開 **"添加子網"** 視窗。 配置以下設置，然後選擇 **"添加"** 以添加值：
      - **子網名稱**：在此示例中，我們命名子網"FrontEnd"。
      - **子網位址範圍**：此子網的位址範圍。

1. 此時，在 **"安全"** 選項卡上，保留預設值：

   - **DDos 保護**： 基本
   - **防火牆**： 已禁用
1. 選擇 **"查看 + 創建**"以驗證虛擬網路設置。
1. 驗證設置後，選擇 **"創建**"。
