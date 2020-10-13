---
title: 開始使用並建立小組的 Azure 實驗室服務實驗室
description: 瞭解如何開始使用並建立小組的 Azure 實驗室服務實驗室。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: b585196fe61a09697cfa203aaa33f08afae2b427
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946620"
---
# <a name="get-started-and-create-a-lab-services-lab-from-teams"></a>開始使用並從小組建立實驗室服務實驗室

本文說明如何將 Azure 實驗室服務應用程式新增至小組。 然後，如何從小組建立實驗室。

## <a name="add-a-lab-services-app-to-teams"></a>將實驗室服務應用程式新增至小組

您可以直接在小組頻道中新增實驗室服務，然後讓小組中的每個人都能使用此應用程式。 遵循下列三個步驟：

1. 流覽至您想要新增應用程式的小組頻道，然後 **+** 按一下 [...]，以選取要新增索引標籤。從右邊視窗的頂端。 
1. 從 [索引標籤選項] 搜尋 **Azure 實驗室服務** ，然後新增此應用程式。 

    > [!NOTE]
    > 只有小組 **擁有** 者能夠為小組建立實驗室。
1. 選取您要在此小組中用來建立教室實驗室的實驗室服務帳戶。 

    Azure 實驗室服務會使用 [Azure 實驗室服務網站](https://labs.azure.com) 的單一登入，並提取您有權存取的所有實驗室帳戶。 

    系統會顯示與小組位於相同租使用者中的帳戶，以及您 **擁有擁有**者、 **參與者**或建立 **者** 存取權的帳戶。 

   ![歡迎使用 ALS](./media/integrate-with-teams/welcome.png) 
1. 按下 [ **儲存** ]，然後將應用程式加入至 [小組]，然後將索引標籤新增至通道。 

    現在您可以從通道中選取 [ **Azure 實驗室服務** ] 索引標籤，然後開始管理實驗室，如下列步驟所述。

    一旦有小組成員新增索引標籤，該索引標籤就會對通道中的每個人顯示。 任何具有此應用程式存取權的使用者均可透過其用於 Microsoft Teams 的認證來取得單一登入存取。 任何不具此應用程式存取權的使用者會在 Teams 中看到此索引標籤，但是在您提供內部部署應用程式和應用程式之 Azure 入口網站發佈版本的權限以前，都會遭到封鎖。

## <a name="create-a-classroom-lab"></a>建立教室實驗室

選取實驗室帳戶之後，小組擁有者將能夠為小組建立實驗室。 整個實驗室建立程式和實驗室層級的所有工作都可在小組內執行。 使用者可以選擇在相同的小組內建立多個實驗室，而小組擁有者在實驗室帳戶層級具有適當的存取權，只會看到與特定小組相關聯的實驗室。

## <a name="giving-access-to-users-of-the-lab-account"></a>為實驗室帳戶的使用者提供存取權

您必須在 [Azure](https://ms.portal.azure.com/) 入口網站中，對實驗室帳戶層級的使用者提供存取權。

1. 在 Azure 入口網站中，流覽至您的 Azure 實驗室服務帳戶。 
1. 在 [實驗室帳戶] 頁面上，選取 [存取控制 (IAM)]，選取工具列上的 [+新增]，然後選取工具列上的 [+新增角色指派]。 

    ![[存取控制] -> [新增角色指派] 按鈕](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. 在 [新增角色指派] 頁面上，針對 [角色] 選取 [實驗室建立者]，選取您要新增到實驗室建立者角色的使用者，然後選取 [儲存]。 

    ![新增實驗室建立者](./media/tutorial-setup-lab-account/add-lab-creator.png)

### <a name="creating-classroom-labs"></a>建立教室實驗室

無論您是從小組或 [實驗室服務網站](https://labs.azure.com)建立實驗室，課堂實驗室建立程式都一樣。 

如需有關設定此文章的詳細資訊，請參閱實驗室建立流程： [管理 Azure 實驗室服務中的教室實驗室](how-to-manage-classroom-labs.md)。

## <a name="deleting-classroom-labs"></a>正在刪除教室實驗室

您可以藉由直接刪除實驗室（如[管理 Azure 實驗室服務中的教室實驗室](how-to-manage-classroom-labs.md)中所述），在[實驗室服務網站](https://labs.azure.com)中刪除小組內建立的實驗室。 

刪除小組時，也會觸發實驗室刪除。 如果已刪除實驗室建立所在的小組，則會在觸發自動使用者清單同步處理之後24小時自動刪除實驗室。 

刪除索引標籤或卸載應用程式不會導致刪除實驗室。 如果刪除索引標籤，除非透過刪除網站上的實驗室或刪除小組，否則小組成員資格清單上的使用者仍然可以存取 [實驗室服務網站](https://labs.azure.com) 上的 vm。 

## <a name="next-steps"></a>後續步驟

在小組內建立實驗室時，會自動填入實驗室使用者清單，並與團隊成員資格同步處理。 小組中的每個人，包括擁有者、成員和來賓都會自動新增至實驗室使用者清單。 Azure 實驗室服務會維護與小組成員資格的同步處理，並每隔24小時觸發一次自動同步處理。 如需詳細資料，請參閱：

[從小組管理實驗室服務使用者清單](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>另請參閱

另請參閱下列文章：

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [從小組管理實驗室服務中的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [從小組建立實驗室服務排程](how-to-create-schedules-within-teams.md)
- [從小組存取實驗室服務中的 VM (student view) ](how-to-access-vm-for-students-within-teams.md)

