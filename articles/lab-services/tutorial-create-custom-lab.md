---
title: 使用 Azure DevTest Labs 建立實驗室 | Microsoft Docs
description: 在本教學課程中，您會使用 Azure 入口網站在 Azure DevTest Labs 中建立實驗室。 實驗室管理員會設定實驗室，在實驗室中建立 VM，然後設定原則。
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 44539c6779afaece6d955a907819ef82d8cd7d5a
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759528"
---
# <a name="tutorial-set-up-a-lab-by-using-azure-devtest-labs"></a>教學課程：使用 Azure DevTest Labs 設定實驗室
在本教學課程中，您會使用 Azure 入口網站建立實驗室。 實驗室管理員會在組織設定實驗室，並在實驗室中建立 VM，然後設定原則。 實驗室使用者 (例如：開發人員和測試人員) 會認領實驗室中的 VM、與之連線並加以使用。 

在本教學課程中，您會執行下列動作：

> [!div class="checklist"]
> * 建立實驗室
> * 將虛擬機器 (VM) 新增至實驗室
> * 將使用者新增至實驗室使用者角色

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

## <a name="create-a-lab"></a>建立實驗室
下列步驟說明如何使用 Azure 入口網站在 Azure DevTest Labs 中建立實驗室。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 從左邊的主功能表中，選取 [建立資源]  (位於清單的頂端)，指向 [開發人員工具]  ，然後按一下 [DevTest Labs]  。 

    ![新的 DevTest Lab 功能表](./media/tutorial-create-custom-lab/new-custom-lab-menu.png)
1. 在 [建立 DevTest Lab]  視窗中，執行下列動作： 
    1. 針對 [實驗室名稱]  ，輸入實驗室的名稱。 
    2. 針對 [訂用帳戶]  ，選取您要在其中建立實驗室的訂用帳戶。 
    3. 針對 [資源群組]  ，選取 [新建]  ，並為資源群組輸入名稱。 
    4. 針對 [位置]  ，選取您要在其中建立實驗室的位置/區域。 
    5. 選取 [建立]  。 
    6. 選取 [釘選到儀表板]  。 建立實驗室之後，實驗室就會出現在儀表板上。 

        ![建立 DevTest Labs 的實驗室區段](./media/tutorial-create-custom-lab/create-custom-lab-blade.png)
2. 查看通知以確認實驗室已順利建立。 選取 [前往資源]  。  

    ![通知](./media/tutorial-create-custom-lab/creation-notification.png)
3. 確認您看到您實驗室的 [DevTest Lab]  頁面。 

    ![您實驗室的首頁](./media/tutorial-create-custom-lab/lab-home-page.png)

## <a name="add-a-vm-to-the-lab"></a>將 VM 新增至實驗室

1. 在 [DevTest Lab]  頁面上，選取工具列上的 [+ 新增]  。 

    ![[新增] 按鈕](./media/tutorial-create-custom-lab/add-vm-to-lab-button.png)
1. 在 [選擇基底]  頁面上，搜尋關鍵字 (例如：Windows、Ubuntu)，並在清單中選取其中一個基礎映像。 
1. 在 [虛擬機器]  頁面上，請執行下列動作： 
    1. 針對 [虛擬機器名稱]  ，輸入虛擬機器的名稱。 
    2. 針對 [使用者名稱]  ，輸入可存取虛擬機器的使用者名稱。 
    3. 針對 [密碼]  ，輸入使用者的密碼。 

        ![選擇基底](./media/tutorial-create-custom-lab/new-virtual-machine.png)
1. 選取 [進階設定]  索引標籤。
    1. 針對 [讓此機器接受認領]  ，選取 [是]  。
    2. 確認 [執行個體計數]  已設定為 [1]  。 如果您將其設定為 [2]  ，則會以下列名稱建立 2 部 VM：`<base image name>00' and <base image name>01`。 例如：`win10vm00` 和 `win10vm01`。     
    3. 選取 [提交]  。 

        ![選擇基底](./media/tutorial-create-custom-lab/new-vm-advanced-settings.png)
    9. 您會看到 VM 狀態出現在 [可認領虛擬機器]  清單中。 建立虛擬機器可能需要大約 25 分鐘的時間。 VM 會建立在不同的 Azure 資源群組中，其名稱開頭會是目前具有實驗室的資源群組名稱。 例如，如果實驗室位於 `labrg`，則可能會在資源群組 `labrg3988722144002` 中建立 VM。 

        ![VM 建立狀態](./media/tutorial-create-custom-lab/vm-creation-status.png)
1. 建立 VM 之後，您看到它出現在 [可認領虛擬機器]  清單中。 

    > [!NOTE] 
    > 在 [進階設定]  頁面上，您可以設定 VM 的公用、私人或共用 IP 位址。 若已啟用**共用 IP**，Azure DevTest Labs 會自動啟用適用於 Windows VM 的 RDP 和適用於 Linux VM 的 SSH。 如果您建立具有**公用 IP** 位址的 VM，則會啟用 RDP 和 SSH (不需從 DevTest Labs 進行任何變更)。  

## <a name="add-a-user-to-the-lab-user-role"></a>將使用者新增至實驗室使用者角色

1. 在左側功能表中，選取 [組態和原則]  。 

    ![組態和原則](./media/tutorial-create-custom-lab/configuration-and-policies-menu.png)
1. 從功能表中選取 [存取控制 (IAM)]  ，然後選取工具列上的 [+ 新增角色指派]  。 

    ![新增角色指派 - 按鈕](./media/tutorial-create-custom-lab/add-role-assignment-button.png)
1. 在 [新增權限]  頁面上，執行下列動作：
    1. 針對 [角色]  ，選取 [DevTest Labs 使用者]  。 
    2. 選取您想要新增的**使用者**。 
    3. 選取 [儲存]  。

        ![新增使用者](./media/tutorial-create-custom-lab/add-user.png)

## <a name="clean-up-resources"></a>清除資源
下一個教學課程會示範實驗室使用者可以如何認領和連線到實驗室中的 VM。 如果您不想進行該教學課程，並且想清除本教學課程中建立的資源，請遵循下列步驟： 

1. 在 Azure 入口網站中，選取功能表中的 [資源群組]  。 

    ![資源群組](./media/tutorial-create-custom-lab/resource-groups.png)
1. 選取您已在其中建立實驗室的資源群組。 
1. 從工具列中選取 [刪除資源群組]  。 刪除資源群組會刪除群組中的所有資源 (包括實驗室)。 

    ![實驗室資源群組](./media/tutorial-create-custom-lab/lab-resource-group.png)
1. 請重複這些步驟來刪除以 `<your resource group name><random numbers>` 名稱建立的其他資源群組。 例如：`splab3988722144001` 。 VM 會建立在此資源群組中，而不是實驗室所在的資源群組中。 

## <a name="next-steps"></a>後續步驟
在本教學課程中，您已建立具有 VM 的實驗室，並且讓使用者可存取該實驗室。 若要了解如何以實驗室使用者的身分存取實驗室，請前進到下一個教學課程：

> [!div class="nextstepaction"]
> [教學課程：存取實驗室](tutorial-use-custom-lab.md)

