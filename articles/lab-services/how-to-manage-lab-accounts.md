---
title: 管理 Azure 實驗室服務中的實驗室帳戶 | Microsoft Docs
description: 了解如何在 Azure 訂用帳戶中建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 33e5e42f65fdd34bb37b12947b5173700ad7970c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999496"
---
# <a name="create-and-manage-lab-accounts"></a>建立及管理實驗室帳戶
在 Azure Lab Services 中，實驗室帳戶是受控實驗室類型 (例如教室實驗室) 的容器。 系統管理員會使用 Azure 實驗室服務來設定實驗室帳戶，並向可以在帳戶中建立實驗室的實驗室擁有者提供存取權。 本文說明如何建立實驗室帳戶、檢視所有實驗室帳戶，或刪除實驗室帳戶。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 選取 [DevOps] 區段中的 [實驗室帳戶]。 如果您選取 [實驗室帳戶] 旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛] 區段。 從下一次開始，您可選取 [我的最愛] 下方的 [實驗室帳戶]。

    ![[所有服務] -> [實驗室帳戶]](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在 [實驗室帳目] 頁面上，選取工具列上的 [新增]或頁面上的 [建立實驗室帳戶]。 

    ![在 [實驗室帳戶] 頁面上選取 [新增]](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在 [建立實驗室帳戶] 頁面的 [基本資訊] 索引標籤上，執行下列動作： 
    1. 針對 [實驗室帳戶名稱] 輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]，選取 [新建]，並為資源群組輸入名稱。
    4. 針對 [位置]，選取您要在其中建立實驗室帳戶的位置/區域。
    5. 在 [允許實驗室建立者挑選實驗室位置] 欄位中，指定您是否要讓實驗室建立者選取實驗室的位置。 此選項預設為停用。 加以停用時，實驗室建立者將無法為其建立的實驗室指定位置。 實驗室會在最接近實驗室帳戶的地理位置建立。 加以啟用時，實驗室建立者將可在建立實驗室時選取位置。 如需詳細資訊，請參閱[允許實驗室建立者挑選實驗室的位置](allow-lab-creator-pick-lab-location.md)。 

        ![建立實驗室帳戶 -> 基本資料](./media/how-to-manage-lab-accounts/create-lab-account-basics.png)
5. 選取頁面底部的 [下一步:進階]，瀏覽至 [進階] 索引標籤，然後執行下列步驟： 
    1. 選取現有的 **共用映像庫** 或建立一個。 您可以將範本 VM 儲存在共用映像庫中以供其他人重複使用。 如需共用映像庫的詳細資訊，請參閱[在 Azure 實驗室服務中使用共用映像庫](how-to-use-shared-image-gallery.md)。
    2. 指定當使用者中斷連線時，是否要 **自動關閉 Windows 虛擬機器**。 指定虛擬機器在自動關閉之前，應該等候使用者重新連線的時間長度。 
    3. 針對 [對等虛擬網路]，選取實驗室網路的對等虛擬網路 (VNet)。 在此帳戶中建立的實驗室會連線至所選取的 VNet，並具備所選取 VNet 中資源的存取權。 如需詳細資訊，請參閱[連接實驗室的虛擬網路與對等虛擬網路](how-to-connect-peer-virtual-network.md)。    
    8. 為實驗室中的 VM 指定 **位址範圍**。 位址範圍應使用無類別的網域間路由 (CIDR) 標記法 (範例：10.20.0.0/23)。 實驗室中的虛擬機器將會建立在此位址範圍內。 如需詳細資訊，請參閱[為實驗室中的 VM 指定位址範圍](how-to-connect-peer-virtual-network.md#specify-an-address-range-for-vms-in-the-lab-account)  

        > [!NOTE]
        > 只有在針對實驗室啟用 **對等虛擬網路** 時，才會套用 **位址範圍** 屬性。

        ![建立實驗室帳戶 -> 進階](./media/how-to-manage-lab-accounts/create-lab-account-advanced.png)  
6. 選取頁面底部的 [下一步:標籤]，切換至 [標籤] 索引標籤。新增您想要與實驗室帳戶產生關聯的任何標籤。 標籤為成對的名稱和值，可讓您將相同的標籤套用至多個資源與資源群組，以便為資源分類及檢視合併的帳單。 如需詳細資訊，請參閱 [使用標記組織您的 Azure 資源](../azure-resource-manager/management/tag-resources.md)。

    ![顯示 [建立實驗室帳戶] 頁面的螢幕擷取畫面，其中反白顯示 [標記] 索引標籤。](./media/how-to-manage-lab-accounts/create-lab-account-tags.png)
7. 選取此頁面底部的 [檢閱 + 建立]，切換至 [檢閱 + 建立] 索引標籤。 
4. 請參閱此頁面上的摘要資訊，然後選取 [建立]。 

    ![建立實驗室帳戶 -> 標籤](./media/how-to-manage-lab-accounts/create-lab-account-review-create.png)
5. 等到部署完成時，請展開 [後續步驟]，然後選取 [移至資源]，如下圖所示： 

    您也可以選取工具列上的 **鈴鐺圖示** (**通知**)，確認部署成功，然後選取 [移至資源]。 

    或者，選取 [實驗室帳戶] 頁面上的 [重新整理]，然後選取您所建立的實驗室帳戶。 

    ![[建立實驗室帳戶] 視窗](./media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 您會看到下列 [實驗室帳戶] 頁面：

    ![[實驗室帳戶] 頁面](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="view-lab-accounts"></a>檢視實驗室帳戶
1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取功能表中的 [所有資源]。 
3. 針對 [類型]，選取 [實驗室帳戶]。 
    您也可以依訂用帳戶、資源群組、位置和標籤來篩選。 

    ![[所有資源]-> [實驗室帳戶]](./media/how-to-manage-lab-accounts/all-resources-lab-accounts.png)


## <a name="delete-a-lab-account"></a>刪除實驗室帳戶
請遵循上一節以清單顯示實驗室帳戶的指示。 使用下列指示來刪除實驗室帳戶： 

1. 選取您要刪除的 **實驗室帳戶**。 
2. 從工作列中選取 [刪除]。 

    ![[實驗室帳戶]-> [刪除] 按鈕](./media/how-to-manage-lab-accounts/delete-button.png)
1. 輸入 **Yes** 進行確認。
1. 選取 [刪除] 。 

    ![刪除實驗室帳戶 - 確認](./media/how-to-manage-lab-accounts/delete-lab-account-confirmation.png)

> [!NOTE]
> 您也可以使用 Az.LabServices PowerShell 模組 (預覽) 來管理實驗室帳戶。 如需詳細資訊，請參閱 [GitHub 上的 Az.LabServices 首頁](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

## <a name="next-steps"></a>後續步驟
請參閱目錄 (TOC) 的 **操作說明指南** -> **建立和設定實驗室帳戶 (實驗室帳戶擁有者)** 一節中的其他文章。 