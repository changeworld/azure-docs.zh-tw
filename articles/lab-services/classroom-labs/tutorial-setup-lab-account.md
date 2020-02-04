---
title: 使用 Azure 實驗室服務設定實驗室帳戶 | Microsoft Docs
description: 了解如何使用 Azure Lab Services 設定實驗室帳戶、新增實驗室建立者，以及指定實驗室帳戶中的實驗室所要使用的 Marketplace 映像。
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 62ccda96563a53ebc77337bb2f89cfddd4410a26
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719036"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>教學課程：使用 Azure Lab Services 設定實驗室帳戶
在 Azure Lab Services 中，實驗室帳戶會作為中心帳戶，用來管理組織的實驗室。 在您的實驗室帳戶中，您可以為他人授與建立實驗室的權限，以及設定對實驗室帳戶下的所有實驗室皆適用的原則。 在本教學課程中，請了解如何以實驗室管理員的身分建立實驗室帳戶。 

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立實驗室帳戶
> * 將使用者新增至實驗室建立者角色
> * 指定適用於實驗室擁有者的 Marketplace 映像

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]  。 選取 **DEVOPS** 區段中的 [實驗室服務]  。 如果您選取 [實驗室服務]  旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛]  區段。 從下一次開始，您可選取 [我的最愛]  下方的 [實驗室服務]  。

    ![[所有服務] -> [實驗室服務]](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在 [實驗室服務]  頁面上，選取工具列上的 [新增]  。 

    ![在 [實驗室帳戶] 頁面上選取 [新增]](../media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在 [實驗室帳戶]  頁面上，執行下列動作： 
    1. 針對 [實驗室帳戶名稱]  輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]  ，選取 [新建]  ，並為資源群組輸入名稱。
    4. 針對 [位置]  ，選取您要在其中建立實驗室帳戶的位置/區域。 
    5. 選取現有的**共用映像庫**或建立一個。 您可以將範本 VM 儲存在共用映像庫中以供其他人重複使用。 如需共用映像庫的詳細資訊，請參閱[在 Azure 實驗室服務中使用共用映像庫](how-to-use-shared-image-gallery.md)。 
    6. 針對 [對等虛擬網路]  ，選取實驗室網路的對等虛擬網路 (VNet)。 在此帳戶中建立的實驗室會連線至所選取的 VNet，並具備所選取 VNet 中資源的存取權。 
    7. 為實驗室中的 VM 指定**位址範圍**。 位址範圍應使用無類別的網域間路由 (CIDR) 標記法 (範例：10.20.0.0/23)。 實驗室中的虛擬機器將會建立在此位址範圍內。 如需詳細資訊，請參閱[為實驗室中的 VM 指定位址範圍](how-to-configure-lab-accounts.md#specify-an-address-range-for-vms-in-the-lab)。
    8. 在 [允許實驗室建立者挑選實驗室位置]  欄位中，指定您是否要讓實驗室建立者選取實驗室的位置。 此選項預設為停用。 加以停用時，實驗室建立者將無法為其建立的實驗室指定位置。 實驗室會在最接近實驗室帳戶的地理位置建立。 加以啟用時，實驗室建立者將可在建立實驗室時選取位置。 
    9. 選取 [建立]  。 

        ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/lab-account-settings.png)
5. 選取工具列上的**鈴鐺圖示** (**通知**)，確認部署成功，然後選取 [移至資源]  。 

    或者，選取 [實驗室帳戶]  頁面上的 [重新整理]  ，然後選取您所建立的實驗室帳戶。 

    ![[建立實驗室帳戶] 視窗](../media/tutorial-setup-lab-account/go-to-lab-account.png)    
6. 您會看到下列 [實驗室帳戶]  頁面：

    ![[實驗室帳戶] 頁面](../media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者]  角色的成員。 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶建立教室實驗室，您可以略過此步驟。 若要使用其他使用者帳戶來建立教室實驗室，請執行下列步驟： 

若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者]  角色：

1. 在 [實驗室帳戶]  頁面上，選取 [存取控制 (IAM)]  ，選取工具列上的 [+新增]  ，然後選取工具列上的 [+新增角色指派]  。 

    ![[存取控制] -> [新增角色指派] 按鈕](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派]  頁面上，針對 [角色]  選取 [實驗室建立者]  ，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]  。 

    ![新增實驗室建立者](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>指定實驗室建立者可用的 Marketplace 映像
身為實驗室帳戶擁有者的您，可以指定實驗室建立者可用來在實驗室帳戶中建立實驗室的 Marketplace 映像。 

1. 選取左側功能表上的 [Marketplace 映像]  。 根據預設，您會看到映像 (包括已啟用和停用) 的完整清單。 您可以從頂端的下拉式清單中選取 [僅限已啟用]  /[僅限已停用]  選項來篩選清單，而僅檢視已啟用/已停用的映像。 
    
    ![Marketplace 映像頁面](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    在清單中顯示的是滿足下列條件的 Marketplace 映像：
        
    - 建立單一 VM。
    - 使用 Azure Resource Manager 來佈建 VM
    - 不需要購買額外的授權方案
2. 若要**停用**已啟用的 Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)  ，然後選取 [停用映像]  。 

        ![停用一個映像](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [停用選取的映像]  。 

        ![停用多個映像](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. 同樣地，若要**啟用** Marketplace 映像，請執行下列其中一個動作： 
    1. 選取最後一個資料行中的 [...] (省略符號)  ，然後選取 [啟用映像]  。 
    2. 在清單中選取映像名稱前面的核取方塊，以選取清單中的一或多個映像，然後選取 [啟用選取的映像]  。 

## <a name="next-steps"></a>後續步驟
您已在本教學課程中建立實驗室帳戶。 若要了解如何以教師身分建立教室實驗室，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [設定教室實驗室](tutorial-setup-classroom-lab.md)

