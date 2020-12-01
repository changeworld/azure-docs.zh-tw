---
title: 開始使用並在小組內建立 Azure 實驗室服務實驗室
description: 瞭解如何開始使用，並在小組內建立 Azure 實驗室服務實驗室。
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: cc4ad604bdf250cc6e4ba2c50c2f7143c921e906
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96433966"
---
# <a name="get-started-and-create-a-lab-services-lab-within-teams"></a>開始使用並在小組內建立實驗室服務實驗室

本文說明如何將 **Azure 實驗室服務** 應用程式新增至小組，以及如何在 MS 小組環境中建立實驗室。

## <a name="prerequisites"></a>必要條件

在本教學課程中，您會為您的小組設定具有虛擬機器的實驗室。 若要在實驗室帳戶中設定實驗室，您必須是實驗室帳戶中其中一個角色的成員：擁有者、實驗室建立者或參與者。 您用來建立實驗室帳戶的帳戶會自動新增至擁有者角色。 因此，您可以使用您用來建立實驗室帳戶的使用者帳戶來建立實驗室。

以下是在小組內使用 Azure 實驗室服務時的一般工作流程

1. 使用者會在 Azure 入口網站上 [建立實驗室帳戶](tutorial-setup-lab-account.md#create-a-lab-account) 。
1. [實驗室帳戶建立者會將其他使用者新增](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)至 **實驗室建立者** 角色。 例如，實驗室帳戶建立者/管理員將教師新增至 **實驗室建立者** 角色，讓這些教師可為其課程建立實驗室。
1. 然後，教育工作者建立實驗室、預先設定範本 VM，然後發佈實驗室，將 VM 布建到小組的每個人。
1. 一旦發佈實驗室後，就會在第一次登入 Azure 實驗室服務時，將 VM 指派給小組成員資格清單上的每個人，方法是按一下包含小組內的 **Azure Lab services** 應用程式的索引標籤 (SSO) 或存取 [labs 網站](https://labs.azure.com)。 然後，使用者可以使用 VM 來執行類別工作和作業。

> [!IMPORTANT]
> 只有在實驗室帳戶是在與小組相同的租使用者中建立時，才能在小組內使用 Azure 實驗室服務。

## <a name="add-azure-lab-services-app-as-a-tab-to-a-team"></a>將 Azure 實驗室服務應用程式新增為小組的索引標籤

您（小組擁有者）可以直接在您的小組頻道中新增 **Azure 實驗室服務** 應用程式，而該應用程式可供小組中的每個人使用。 遵循下列三個步驟：

1. 流覽至您想要在其中新增應用程式的 [小組] 頻道，然後選取以新增索引標籤 **+** 。 
1. 從 [索引標籤選項] 搜尋 **Azure 實驗室服務** ，然後新增此應用程式。 

    > [!NOTE]
    > 只有小組 **擁有** 者能夠為小組建立實驗室。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/add.png" alt-text="新增索引標籤":::
1. 選取您要在此小組中用來建立實驗室的實驗室服務帳戶。 

    Azure 實驗室服務會使用 [Azure 實驗室服務網站](https://labs.azure.com) 的單一登入，並提取您有權存取的所有實驗室帳戶。 

    系統會顯示與小組位於相同租使用者中的帳戶，以及您 **擁有擁有** 者、 **參與者** 或建立 **者** 存取權的帳戶。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/welcome.png" alt-text="歡迎使用 ALS":::
1. 按下 [ **儲存** ]，然後將索引標籤新增至通道。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/integrate-with-teams/created.png" alt-text="建立 [ALS] 索引標籤":::

    現在您可以從通道中選取 [ **Azure 實驗室服務** ] 索引標籤，然後開始管理實驗室，如下列文章所述。

選取實驗室帳戶之後，小組擁有者將能夠為小組建立實驗室。 整個實驗室建立程式和實驗室層級的所有工作都可在小組內執行。 使用者可以選擇在相同的小組內建立多個實驗室，而小組擁有者在實驗室帳戶層級具有適當的存取權，只會看到與特定小組相關聯的實驗室。

## <a name="next-steps"></a>後續步驟

在小組內建立實驗室時，會自動填入實驗室使用者清單，並與團隊成員資格同步處理。 小組中的每個人，包括擁有者、成員和來賓都會自動新增至實驗室使用者清單。 Azure 實驗室服務會維護與小組成員資格的同步處理，並每隔24小時觸發一次自動同步處理。 如需詳細資料，請參閱：

[管理小組內的實驗室服務使用者清單](how-to-manage-user-lists-within-teams.md)

### <a name="see-also"></a>另請參閱

另請參閱下列文章：

- [在小組內使用 Azure 實驗室服務總覽](lab-services-within-teams-overview.md)
- [管理小組內實驗室的 VM 集區](how-to-manage-vm-pool-within-teams.md)
- [建立及管理小組內的實驗室排程](how-to-create-schedules-within-teams.md)
- [存取小組內的 VM –學生版視圖](how-to-access-vm-for-students-within-teams.md)
- [在小組內刪除實驗室](how-to-delete-lab-within-teams.md)
