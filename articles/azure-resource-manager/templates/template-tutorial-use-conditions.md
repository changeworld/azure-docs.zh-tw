---
title: 使用範本中的條件
description: 深入了解如何根據條件部署 Azure 資源。 說明如何部署新資源或使用現有資源。
author: mumian
ms.date: 05/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 8f51c65489efeed1fa18e70bd75e7370a9e59903
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260617"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>教學課程：使用 ARM 範本中的條件

了解如何根據 Azure Resource Manager (ARM) 範本中的條件來部署 Azure 資源。

在[設定資源部署順序](./template-tutorial-create-templates-with-dependent-resources.md)教學課程中，您建立了虛擬機器、虛擬網路和其他相依資源，包括儲存體帳戶。 您可以不用每次都建立新的儲存體帳戶，而是讓使用者在建立新的儲存體帳戶與使用現有的儲存體帳戶之間做選擇。 為了達成此目標，您會定義額外的參數。 如果參數的值是 "new"，則會建立新的儲存體帳戶。 否則，會使用具有所提供名稱的現有儲存體帳戶。

![Resource Manager 範本使用條件圖](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 修改範本
> * 部署範本
> * 清除資源

本教學課程只會涵蓋使用條件的基本案例。 如需詳細資訊，請參閱

* [範本檔案結構：條件](conditional-resource-deployment.md)。
* [在 ARM 範本中有條件地部署資源](/azure/architecture/building-blocks/extending-templates/conditional-deploy)。
* [範本函式：If](./template-functions-logical.md#if)。
* [ARM 範本的比較函式](./template-functions-comparison.md)

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>Prerequisites

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[使用 Visual Studio Code 建立 ARM 範本](use-vs-code-to-create-template.md)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 ARM 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，選取 [檔案]  >[開啟檔案]  。
1. 在 [檔案名稱]  中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. 選取 [開啟]  以開啟檔案。
1. 範本中定義了六項資源：

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    在自訂範本之前，建議您先檢閱範本參考。

1. 選取 [檔案]  >[另存新檔]  ，以名稱 **azuredeploy.json** 將檔案的複本儲存至您的本機電腦。

## <a name="modify-the-template"></a>修改範本

對現有範本進行兩個變更：

* 新增儲存體帳戶名稱參數。 使用者可以指定新的儲存體帳戶名稱或現有的儲存體帳戶名稱。
* 新增名為 **newOrExisting** 的參數。 部署會使用這個參數來決定要建立新的儲存體帳戶或使用現有儲存體帳戶。

進行變更的程序如下：

1. 在 Visual Studio Code 中，開啟 **azuredeploy.json**。
1. 在整個範本中以 **parameters('storageAccountName')** 來取代三個 **variables('storageAccountName')** 。
1. 移除下列變數定義：

    ![Resource Manager 範本使用條件圖](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. 將下列兩個參數新增至參數區段的開頭：

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    按 **[ALT]+[SHIFT]+F** 在 Visual Studio Code 中格式化範本。

    已更新的參數定義看起來如下：

    ![Resource Manager 使用條件](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. 將下列行新增至儲存體帳戶定義的開頭。

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    條件會檢查名為 **newOrExisting** 的參數值。 如果參數值是 **new**，則部署會建立儲存體帳戶。

    已更新的儲存體帳戶定義看起來如下：

    ![Resource Manager 使用條件](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. 使用下列值更新虛擬機器資源定義的 **storageUri** 屬性：

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    如果您要使用不同資源群組下的現有儲存體帳戶，就必須進行此變更。

1. 儲存變更。

## <a name="deploy-the-template"></a>部署範本

請遵循[部署範本](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template)中的指示，來開啟 Cloud Shell 並上傳修改過的範本，然後執行下列 PowerShell 指令碼來部署範本。

> [!IMPORTANT]
> 儲存體帳戶名稱必須是 Azure 中是獨一無二的。 名稱必須只有小寫字母或數字。 名稱長度不得超過 24 個字元。 儲存體帳戶名稱是附加 "store" 的專案名稱。 請確定專案名稱和所產生的儲存體帳戶名稱符合儲存體帳戶名稱需求。

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
$newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
$location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
$vmAdmin = Read-Host -Prompt "Enter the admin username"
$vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUsername $vmAdmin `
    -adminPassword $vmPassword `
    -dnsLabelPrefix $dnsLabelPrefix `
    -storageAccountName $storageAccountName `
    -newOrExisting $newOrExisting `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

> [!NOTE]
> 如果 **newOrExisting** 是 **new**，則部署會失敗，但是具有已指定儲存體帳戶名稱的儲存體帳戶已存在。

請嘗試將 **newOrExisting** 設為 "existing" 並且指定現有儲存體帳戶，來進行另一個部署。 若要事先建立儲存體帳戶，請參閱[建立儲存體帳戶](../../storage/common/storage-account-create.md)。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。 若要刪除資源群組，請選取 [試試看]  來開啟 Cloud Shell。 若要貼上 PowerShell 指令碼，請以滑鼠右鍵按一下 Shell 窗格，然後選取 [貼上]  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您開發了範本，讓使用者在建立新儲存體帳戶與使用現有儲存體帳戶之間做選擇。 若要深入了解如何從 Azure Key Vault 擷取祕密，並且在範本部署中使用祕密作為密碼，請參閱：

> [!div class="nextstepaction"]
> [在範本部署中整合 Key Vault](./template-tutorial-use-key-vault.md)
