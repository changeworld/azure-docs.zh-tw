---
title: 建立服務端點原則並為其建立關聯 - Azure 入口網站
titlesuffix: Azure Virtual Network
description: 在本文中，了解如何使用 Azure 入口網站設定服務端點原則並為其建立關聯。
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651139"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>使用 Azure 入口網站建立、變更或刪除服務端點原則

服務端點原則可讓您透過服務端點來篩選特定 Azure 資源的虛擬網路流量。 如果您不熟悉服務端點原則，請參閱[服務端點原則概觀](virtual-network-service-endpoint-policies-overview.md)以深入了解。

 在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立服務端點原則
> * 建立服務端點原則定義
> * 建立具有子網路的虛擬網路
> * 將服務端點原則建立關聯至子網路

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure 

在 https://portal.azure.com 登入 Azure 入口網站。

## <a name="create-a-service-endpoint-policy"></a>建立服務端點原則

1. 選取 Azure 入口網站左上角的 [+ 建立資源]****。
2. 在搜索窗格中，鍵入"服務終結點策略"，然後選擇 **"服務終結點策略**"，然後選擇"**創建**"。

![創建服務終結點策略](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. 從 [基本]**** 中，輸入或選取以下資訊 

   - 訂閱：為策略選擇訂閱
   - 資源組：選擇 **"創建新"** 並輸入*我的資源組*
   - 名稱：myEndpointPolicy
   - 地點 ： 美國中部
 
   ![建立服務端點原則基本事項](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. 選擇 **= 在****"資源**"下添加，並在 **"添加資源**窗格"中輸入或選擇以下資訊

   - 服務 ：只有**Microsoft.存儲**可用於服務終結點策略
   - 範圍：從**單一帳戶**中選擇一個，**訂閱中的所有帳戶****和資源組中的所有帳戶**
   - 訂用帳戶：為儲存體帳戶選取訂用帳戶。 原則和儲存體帳戶可以位於不同的訂用帳戶中。
   - 資源群組：選取您的資源群組。 如果範圍設為「資源群組中的所有帳戶」或「單一帳戶」，則為必要項目。  
   - 資源：在選定的訂閱或資源組下選擇 Azure 存儲資源
   - 按一下底部的 **"添加"** 按鈕以完成資源添加

   ![服務終結點策略定義 - 資源](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - 根據需要重複上述步驟添加更多資源

5. 可選：在**標記**中輸入或選擇以下資訊：
   
   - 索引鍵：為您的原則選取索引鍵。 例如：部門     
   - 值：為索引建輸入值組。 例如：財務

6. 選擇 **"審閱 " 創建**。 驗證資訊，然後按一下 [建立]****。 若要進一步編輯，請按一下 [上一步]****。 

   ![建立服務端點原則定義最終驗證](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>檢視端點原則 

1. 在入口網站的 [所有服務]** 方塊中，開始輸入*服務端點原則*。 選擇**服務終結點策略**。
2. 在 [訂用帳戶]**** 下，選取您的訂用帳戶與資源群組，如下圖所示

   ![顯示原則](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. 選取原則，然後按一下 [原則定義]****，以檢視或新增更多原則定義。

   ![顯示原則定義](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. 選取 [相關聯的子網路]**** 以檢視與原則相關聯的子網路。 如果尚未關聯任何子網，請按照下一步中的說明操作。

   ![關聯的子網](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. 將原則建立關聯至子網路

>[!WARNING] 
> 在將策略關聯到給定的子網之前，請確保從子網訪問的所有資源都添加到策略定義中。 策略關聯後，僅允許通過服務終結點訪問*允許列出的*資源。 
>
> 還確保與服務終結點策略關聯的子網中不存在託管 Azure 服務

- 必須先創建虛擬網路和子網，然後才能將策略關聯到子網。 請參閱[創建虛擬網路](./quick-create-portal.md)文章，以尋求説明。

- 設置虛擬網路和子網後，需要為 Azure 存儲配置虛擬網路服務終結點。 在虛擬網路邊欄選項卡上，選擇**服務終結點**，並在下一個窗格中選擇**Microsoft.Storage，** 並在**子網**下選擇所需的 VNet 或子網

- 現在，如果已在為子網佈建服務終結點之前已創建了服務終結點策略，則可以選擇從上述窗格的下拉清單中選擇服務終結點策略，如下所示

    ![創建服務終結點時關聯子網](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- 或者，如果您在配置了服務終結點後關聯服務終結點策略，則可以選擇通過導航到 **"關聯子網"** 窗格來從服務終結點策略邊欄選項卡中關聯子網，如下所示

    ![通過 SEP 關聯子網](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>根據此子網的服務終結點策略，將限制對所有區域中的 Azure 存儲資源的訪問。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立服務端點原則，並將其與子網路產生關聯。 若要深入了解服務端點原則，請參閱[服務端點原則概觀](virtual-network-service-endpoint-policies-overview.md)。
