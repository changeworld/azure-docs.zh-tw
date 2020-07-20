---
title: 使用 Azure 實驗室服務設定實驗室帳戶 | Microsoft Docs
description: 了解如何使用 Azure Lab Services 設定實驗室帳戶、新增實驗室建立者，以及指定實驗室帳戶中的實驗室所要使用的 Marketplace 映像。
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: fba4dbc5386407bd796606d86a5b7bdc7c10fd61
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445061"
---
# <a name="tutorial-set-up-a-lab-account-with-azure-lab-services"></a>教學課程：使用 Azure Lab Services 設定實驗室帳戶
在 Azure Lab Services 中，實驗室帳戶會作為中心帳戶，用來管理組織的實驗室。 在您的實驗室帳戶中，您可以為他人授與建立實驗室的權限，以及設定對實驗室帳戶下的所有實驗室皆適用的原則。 請在本教學課程中了解如何建立實驗室帳戶。 

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立實驗室帳戶
> * 將使用者新增至實驗室建立者角色

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-lab-account"></a>建立實驗室帳戶
下列步驟將說明如何透過 Azure 入口網站使用 Azure 實驗室服務，來建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取左側功能表上的 [所有服務]。 從 [類別] 選取 [DevOps]。 然後，選取 [實驗室服務]。 如果您選取 [實驗室服務] 旁邊的星號 (`*`)，它會新增到左功能表上的 [我的最愛] 區段。 從下一次開始，您可選取 [我的最愛] 下方的 [實驗室服務]。

    ![[所有服務] -> [實驗室服務]](./media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. 在 [實驗室服務] 頁面上，選取工具列上的 [新增]，或選取頁面上的 [建立實驗室帳戶] 按鈕。 

    ![在 [實驗室帳戶] 頁面上選取 [新增]](./media/tutorial-setup-lab-account/add-lab-account-button.png)
4. 在 [建立實驗室帳戶] 頁面的 [基本資訊] 索引標籤上，執行下列動作： 
    1. 針對 [實驗室帳戶名稱] 輸入名稱。 
    2. 選取您要在其中建立實驗室帳戶的 **Azure 訂用帳戶**。
    3. 針對 [資源群組]，請選取現有資源群組或選取 [新建]，然後為資源群組輸入名稱。
    4. 針對 [位置]，選取您要在其中建立實驗室帳戶的位置/區域。 

        ![實驗室帳戶 - 基本資訊頁面](./media/tutorial-setup-lab-account/lab-account-basics-page.png)
    5. 選取 [檢閱 + 建立]。
    6. 檢閱摘要，然後選取 [建立]。 

        ![檢閱 + 建立 -> 建立](./media/tutorial-setup-lab-account/create-button.png)    
5. 部署完成時，請展開 [後續步驟]，然後選取 [移至資源]。 

    ![移至實驗室帳戶頁面](./media/tutorial-setup-lab-account/go-to-lab-account.png)
6. 確認您有看到 [實驗室帳戶] 頁面。 

    ![[實驗室帳戶] 頁面](./media/tutorial-setup-lab-account/lab-account-page.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>將使用者新增至實驗室建立者角色
若要在實驗室帳戶中設定教室實驗室，使用者必須是實驗室帳戶中的 [實驗室建立者] 角色的成員。 若要提供授課者為其班級建立實驗室的權限，請將他們新增至 [實驗室建立者] 角色：

> [!NOTE]
> 您用來建立實驗室帳戶的帳戶會自動新增至此角色。 如果您打算使用相同的使用者帳戶在本教學課程中建立教室實驗室，請略過此步驟。 

1. 在 [實驗室帳戶] 頁面上，選取 [存取控制 (IAM)]，選取工具列上的 [+新增]，然後選取工具列上的 [+新增角色指派]。 

    ![[存取控制] -> [新增角色指派] 按鈕](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![新增實驗室建立者](./media/tutorial-setup-lab-account/add-lab-creator.png)


## <a name="next-steps"></a>後續步驟
您已在本教學課程中建立實驗室帳戶。 若要了解如何以教師身分建立教室實驗室，請前往下一個教學課程：

> [!div class="nextstepaction"]
> [設定教室實驗室](tutorial-setup-classroom-lab.md)

