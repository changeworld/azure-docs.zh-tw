---
title: 管理 Azure 實驗室服務中的實驗室帳戶 | Microsoft Docs
description: 了解如何在 Azure 訂用帳戶中建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: c82b5d02ab3928eb0472f2a047cdca2238bf0b63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284287"
---
# <a name="manage-lab-accounts-in-azure-lab-services"></a>管理 Azure 實驗室服務中的實驗室帳戶 
在 Azure 實驗室服務中，實驗室帳戶是託管實驗室類型的容器，如課堂實驗室。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 本文說明如何建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]****。 在**DevOps**部分中選擇**實驗室帳戶**。 如果您選取 [實驗室帳戶]**** 旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛]**** 區段。 從下一次開始，您可選取 [我的最愛]**** 下方的 [實驗室帳戶]****。

    ![[所有服務] -> [實驗室帳戶]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在 **"實驗室帳戶"** 頁上，在工具列上選擇 **"添加**"或 **"在頁面上創建實驗室帳戶**"。 

    ![在 [實驗室帳戶] 頁面上選取 [新增]](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在 [建立實驗室帳戶]**** 頁面的 [基本資訊]**** 索引標籤上，執行下列動作： 
    1. 針對 [實驗室帳戶名稱]**** 輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]****，選取 [新建]****，並為資源群組輸入名稱。
    4. 針對 [位置]****，選取您要在其中建立實驗室帳戶的位置/區域。
    5. 在 [允許實驗室建立者挑選實驗室位置]**** 欄位中，指定您是否要讓實驗室建立者選取實驗室的位置。 此選項預設為停用。 加以停用時，實驗室建立者將無法為其建立的實驗室指定位置。 實驗室會在最接近實驗室帳戶的地理位置建立。 加以啟用時，實驗室建立者將可在建立實驗室時選取位置。 有關詳細資訊，請參閱[允許實驗室建立者選擇實驗室的位置](allow-lab-creator-pick-lab-location.md)。 

        ![創建實驗室帳戶 -> 基礎知識](../media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. 選擇 **"下一步"：在**頁面底部前進以導航到 **"高級"** 選項卡，然後執行以下步驟： 
    1. 選取現有的**共用映像庫**或建立一個。 您可以將範本 VM 儲存在共用映像庫中以供其他人重複使用。 如需共用映像庫的詳細資訊，請參閱[在 Azure 實驗室服務中使用共用映像庫](how-to-use-shared-image-gallery.md)。
    2. 指定使用者斷開斷開 Windows 虛擬機器的連接時是否要**自動關閉**Windows 虛擬機器。 指定虛擬機器在自動關閉之前等待使用者重新連接的時間。 
    3. 針對 [對等虛擬網路]****，選取實驗室網路的對等虛擬網路 (VNet)。 在此帳戶中建立的實驗室會連線至所選取的 VNet，並具備所選取 VNet 中資源的存取權。 有關詳細資訊，請參閱[將實驗室的虛擬網路與對等虛擬網路連接](how-to-connect-peer-virtual-network.md)。    
    8. 為實驗室中的 VM 指定**位址範圍**。 位址範圍應位於無類域間路由 （CIDR） 標記法中（例如：10.20.0.0/23）。 實驗室中的虛擬機器將會建立在此位址範圍內。 有關詳細資訊，請參閱[在實驗室中為 VM 指定位址範圍](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)  

        ![創建實驗室帳戶 ->高級](../media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. 選擇 **"下一步"：頁面底部的標記**以切換到 **"標記"** 選項卡。添加要與實驗室帳戶關聯的任何標記。 標記是名稱/值對，使您能夠通過將同一標記應用於多個資源和資源組來對資源進行分類並查看合併計費。 有關詳細資訊，請參閱[使用標記來組織 Azure 資源](../../azure-resource-manager/management/tag-resources.md)。

    ![創建實驗室帳戶 -> 標籤](../media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. 選擇 **"審閱 +** 在此頁面底部創建"以切換到 **"審閱 + 創建**"選項卡。 
4. 查看此頁面上的摘要資訊，然後選擇 **"創建**"。 

    ![創建實驗室帳戶 -> 標籤](../media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. 等待部署完成，展開**後續步驟**，然後選擇"**轉到資源**"，如下圖所示： 

    您還可以選擇工具列上的**鈴形圖示**（**通知**），確認部署成功，然後選擇 **"轉到資源**"。 

    或者，選取 [實驗室帳戶]**** 頁面上的 [重新整理]****，然後選取您所建立的實驗室帳戶。 

    ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 您會看到下列 [實驗室帳戶]**** 頁面：

    ![[實驗室帳戶] 頁面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>檢視實驗室帳戶
1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選取功能表中的 [所有資源]****。 
3. 針對 [類型]****，選取 [實驗室帳戶]****。 
    您也可以依訂用帳戶、資源群組、位置和標籤來篩選。 

    ![[所有資源]-> [實驗室帳戶]](../media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)

## <a name="view-and-manage-labs-in-the-lab-account"></a>檢視及管理實驗室帳戶中的實驗室

1. 在 **"實驗室帳戶"** 頁上，選擇左側功能表上**的所有實驗室**。

    ![帳戶中的實驗室](../media/how-to-manage-lab-accounts/labs-in-account.png)
1. 您會看到帳戶中的**實驗室清單**以及下列資訊： 
    1. 實驗室名稱。
    2. 建立實驗室的日期。 
    3. 建立實驗室之使用者的電子郵件地址。 
    4. 實驗室中允許的使用者數目上限。 
    5. 實驗室的狀態。 
    6. 角色指派。 

## <a name="delete-a-lab-in-the-lab-account"></a>刪除實驗室帳戶中的實驗室
請遵循上一節的指示，以查看實驗室帳戶中的實驗室清單。

1. 選取 **... (省略符號)**，然後選取 [刪除]****。 

    ![刪除實驗室 - 按鈕](../media/how-to-manage-lab-accounts/delete-lab-button.png)
2. 在警告訊息上選取 [是]****。 

    ![確認實驗室刪除](../media/how-to-manage-lab-accounts/confirm-lab-delete.png)

## <a name="delete-a-lab-account"></a>刪除實驗室帳戶
請遵循上一節以清單顯示實驗室帳戶的指示。 使用下列指示來刪除實驗室帳戶： 

1. 選取您要刪除的**實驗室帳戶**。 
2. 從工作列中選取 [刪除]****。 

    ![[實驗室帳戶]-> [刪除] 按鈕](../media/how-to-manage-lab-accounts/delete-button.png)
1. 輸入 **Yes** 進行確認。
1. 選擇 **"刪除**"。 

    ![刪除實驗室帳戶 - 確認](../media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> 您還可以使用 Az.LabServices PowerShell 模組（預覽版）來管理實驗室帳戶。 有關詳細資訊，請參閱[GitHub 上的 Az.LabServices 主頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [允許實驗室建立者挑選實驗室位置](allow-lab-creator-pick-lab-location.md)
- [將實驗室網路與對等虛擬網路連接](how-to-connect-peer-virtual-network.md)
- [將共用圖像庫附加到實驗室](how-to-attach-detach-shared-image-gallery.md)
- [將使用者添加為實驗室擁有者](how-to-add-user-lab-owner.md)
- [查看實驗室的防火牆設置](how-to-configure-firewall-settings.md)
- [為實驗室配置其他設置](how-to-configure-lab-accounts.md)