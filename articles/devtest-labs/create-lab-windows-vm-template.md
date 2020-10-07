---
title: 使用 Azure DevTest Labs 和 Azure Resource Manager 範本建立實驗室
description: 在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本) 在 Azure DevTest Labs 中建立實驗室。 實驗室管理員會設定實驗室，在實驗室中建立 VM，然後設定原則。
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 06/26/2020
ms.openlocfilehash: 2b825b4d4485f401199556b6faaef0017f583cc1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461184"
---
# <a name="quickstart-set-up-a-lab-by-using-azure-devtest-labs-arm-template"></a>快速入門：使用 Azure DevTest Labs ARM 範本設定實驗室
在本快速入門中，您會使用 Azure Resource Manager 範本 (ARM 範本)，以 Windows Server 2019 Datacenter VM 建立實驗室。 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

在本快速入門中，您會執行下列動作：

> [!div class="checklist"]
> * 檢閱範本 
> * 部署範本
> * 確認範本
> * 清除資源

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="review-the-template"></a>檢閱範本

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-dtl-create-lab-windows-vm/)。

:::code language="json" source="~/quickstart-templates/101-dtl-create-lab-windows-vm/azuredeploy.json":::

範本中定義的資源包括：

- [**Microsoft.DevTestLab/labs**](/azure/templates/microsoft.devtestlab/labs)
- [**Microsoft.DevTestLab labs/virtualnetworks**](/azure/templates/microsoft.devtestlab/labs/virtualnetworks)
- [**Microsoft.DevTestLab labs/virtualmachines**](/azure/templates/microsoft.devtestlab/labs/virtualmachines)

若要尋找更多範本範例，請參閱 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devtestlab)。

## <a name="deploy-the-template"></a>部署範本
若要自動執行部署，請按一下下列按鈕。 

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-dtl-create-lab-windows-vm%2Fazuredeploy.json)

1. 建立**新的資源群組**，以便稍後可以輕鬆清除。
1. 選取資源群組的**位置**。 
1. 輸入**實驗室名稱**。 
1. 輸入 **VM 名稱**。 
1. 輸入可存取 VM 的**使用者名稱**。 
1. 輸入使用者的 [密碼]。 
1. 選取 [我同意上方所述的條款及條件]。 
1. 接著，選取 [購買]。

    :::image type="content" source="./media/create-lab-windows-vm-template/deploy-template-page.png" alt-text="部署範本頁面":::

## <a name="validate-the-deployment"></a>驗證部署
1. 選取頂端的 [通知]，查看部署狀態，再按一下**部署進行中**連結。

    :::image type="content" source="./media/create-lab-windows-vm-template/deployment-notification.png" alt-text="部署範本頁面":::
2. 在 [部署 - 概觀] 頁面中，等待部署完成。 這類型的作業 (尤其是建立 VM) 需要一些時間才能完成。 然後，選取您的 [移至資源群組] 或 [資源群組名稱]，如下圖所示： 

    :::image type="content" source="./media/create-lab-windows-vm-template/navigate-resource-group.png" alt-text="部署範本頁面":::
3. 在 [資源群組] 頁面上，您會看到資源群組中的資源清單。 確認您在資源中看到類型為 `DevTest Lab` 的實驗室。 您也會在資源群組中看到相依的資源，例如虛擬網路和虛擬機器。 

    :::image type="content" source="./media/create-lab-windows-vm-template/resource-group-home-page.png" alt-text="部署範本頁面":::
4. 從資源清單中選取您的實驗室，以查看實驗室的首頁。 確認您在**我的虛擬機器**清單中看到 Windows Server 2019 Datacenter VM。 在下圖中，**Essentials** 區段會最小化。 

    :::image type="content" source="./media/create-lab-windows-vm-template/lab-home-page.png" alt-text="部署範本頁面":::

    > [!IMPORTANT] 
    > 讓此頁面保持開啟，並遵循下一節中的指示來清除資源，以避免在 Azure 上執行實驗室和 VM 可能產生的成本。 如果要進行下一個教學課程來測試實驗室中 VM 的存取權，請在完成該教學課程之後清除資源。 

## <a name="clean-up-resources"></a>清除資源

1. 首先請刪除實驗室，以便刪除資源群組。 您無法刪除具有實驗室的資源群組。 若要刪除實驗室，請選取工具列上的 [刪除]。 

    :::image type="content" source="./media/create-lab-windows-vm-template/delete-lab-button.png" alt-text="部署範本頁面":::
 2. 在確認頁面上，輸入**實驗室名稱**，然後選取 [刪除]。 
 3. 等待系統刪除實驗室。 選取**小鐘**圖示，以查看刪除作業的通知。 這個程序需要一些時間。 確認實驗室已刪除，然後在階層連結功能表上選取 [資源群組]。 
 
    :::image type="content" source="./media/create-lab-windows-vm-template/confirm-lab-deletion.png" alt-text="部署範本頁面":::
 1. 在 [資源群組] 頁面中，從工具列選取 [刪除資源群組]。 在確認頁面上，輸入**資源群組名稱**，然後選取 [刪除]。 檢查通知以確認資源群組已刪除。
 
    :::image type="content" source="./media/create-lab-windows-vm-template/delete-resource-group-button.png" alt-text="部署範本頁面":::

## <a name="next-steps"></a>後續步驟
在本快速入門中，您已建立具有 VM 的實驗室。 若要了解如何存取實驗室，請前進到下一個教學課程：

> [!div class="nextstepaction"]
> [教學課程：存取實驗室](tutorial-use-custom-lab.md)
