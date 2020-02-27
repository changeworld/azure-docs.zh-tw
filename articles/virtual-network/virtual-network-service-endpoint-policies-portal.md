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
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/27/2020
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

1. 選取 Azure 入口網站左上角的 [+ 建立資源]。
2. 在 [搜尋] 窗格中，輸入「服務端點原則」並選取 [**服務端點原則**]，然後選取 [**建立**]。

![建立服務端點原則](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. 從 [基本] 中，輸入或選取以下資訊 

   - 訂用帳戶：選取您的訂用帳戶作為原則
   - 資源群組：選取 [**新建**] 並輸入*myResourceGroup*
   - 名稱：myEndpointPolicy
   - 位置：美國中部
 
   ![建立服務端點原則基本事項](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. 選取 [**資源**] 下的 [ **+ 新增**]，然後在 [**新增資源**] 窗格中輸入或選取下列資訊

   - 服務：只有**Microsoft 的儲存體**可供服務端點原則使用
   - 範圍：選取 [**單一帳戶**]、[**訂用帳戶中的所有帳戶**] 和 [**資源群組中的所有帳戶**]
   - 訂用帳戶：為儲存體帳戶選取訂用帳戶。 原則和儲存體帳戶可以位於不同的訂用帳戶中。
   - 資源群組：選取您的資源群組。 如果範圍設為「資源群組中的所有帳戶」或「單一帳戶」，則為必要項目。  
   - 資源：在選取的訂用帳戶或資源群組底下選取您的 Azure 儲存體資源
   - 按一下底部的 [**新增**] 按鈕以完成新增資源

   ![服務端點原則定義-資源](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - 視需要重複上述步驟來新增更多資源

5. 選擇性：在 [標記] 中，輸入或選取下列資訊：
   
   - 索引鍵：為您的原則選取索引鍵。 例如：部門     
   - 值：為索引建輸入值組。 例如：財務

6. 選取 [檢閱 + 建立]。 驗證資訊，然後按一下 [建立]。 若要進一步編輯，請按一下 [上一步]。 

   ![建立服務端點原則定義最終驗證](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>檢視端點原則 

1. 在入口網站的 [所有服務] 方塊中，開始輸入*服務端點原則*。 選取 [**服務端點原則**]。
2. 在 [訂用帳戶] 下，選取您的訂用帳戶與資源群組，如下圖所示

   ![顯示原則](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. 選取原則，然後按一下 [原則定義]，以檢視或新增更多原則定義。

   ![顯示原則定義](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. 選取 [相關聯的子網路] 以檢視與原則相關聯的子網路。 如果尚未關聯任何子網，請遵循下一個步驟中的指示。

   ![關聯的子網](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. 將原則建立關聯至子網路

>[!WARNING] 
> 請確定從子網存取的所有資源都已新增至原則定義，然後才將該原則關聯至指定的子網。 一旦與原則相關聯，就會允許透過服務端點存取*允許列出*的資源。 
>
> 此外，請確定與服務端點原則相關聯的子網中沒有受管理的 Azure 服務

- 您必須先建立虛擬網路和子網，才能讓原則與子網建立關聯。 如需協助，請參閱[建立虛擬網路一](./quick-create-portal.md)文。

- 設定虛擬網路和子網之後，您必須為 Azure 儲存體設定虛擬網路服務端點。 在 [虛擬網路] 分頁上選取 [**服務端點**]，然後在下一個窗格中選取 [ **Microsoft** ]，然後在 [**子網**] 底下選取所需的 VNet 或子網

- 現在，如果您在設定子網的服務端點之前已建立服務端點原則，您可以選擇從上述窗格的下拉式選單中選取服務端點原則，如下所示

    ![建立服務端點時建立子網的關聯](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- 或者，如果您要在服務端點已設定後建立服務端點原則的關聯，您可以在 [服務端點原則] 分頁中選擇要與子網產生關聯，方法是流覽至 [**關聯的子網**] 窗格，如下所示

    ![將子網與 SEP 建立關聯](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>所有區域中 Azure 儲存體資源的存取權，將受限於此子網中的每個服務端點原則。

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立服務端點原則，並將其與子網路產生關聯。 若要深入了解服務端點原則，請參閱[服務端點原則概觀](virtual-network-service-endpoint-policies-overview.md)。
