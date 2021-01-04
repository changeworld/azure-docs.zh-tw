---
title: 具有相依資源的範本
description: 了解如何使用多項資源建立 Azure Resource Manager 範本 (ARM 範本)，以及如何使用 Azure 入口網站加以部署
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d1e5848e568f42fb8a77c65c775962f27a5a03df
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588031"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>教學課程：建立具有相依資源的 ARM 範本

了解如何建立 Azure Resource Manager 範本 (ARM 範本) 以部署多個資源，以及設定部署順序。 在建立範本之後，您可以從 Azure 入口網站使用 Cloud Shell 來部署範本。

在本教學課程中，您會建立儲存體帳戶、虛擬機器、虛擬網路和其他相依資源。 某些資源必須在另一項資源已存在時才能部署。 例如，在虛擬機器的儲存體帳戶和網路介面存在之前，您無法建立虛擬機器。 您可以讓一項資源相依於其他資源，以定義此關聯性。 資源管理員會評估資源之間的相依性，並依其相依順序進行部署。 如果資源並未彼此相依，Resource Manager 就會平行部署資源。 如需詳細資訊，請參閱[定義在 ARM 範本中部署資源的順序](./define-resource-dependency.md)。

![Resource Manager 範本相依資源部署順序圖](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

本教學課程涵蓋下列工作：

> [!div class="checklist"]
> * 開啟快速入門範本
> * 瀏覽範本
> * 部署範本

如果您沒有 Azure 訂用帳戶，請在開始之前先[建立免費帳戶](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必要條件

若要完成本文，您需要：

* Visual Studio Code 搭配 Resource Manager Tools 擴充功能。 請參閱[快速入門：使用 Visual Studio Code 建立 ARM 範本](quickstart-create-templates-use-visual-studio-code.md)。
* 為了提高安全性，請使用為虛擬機器系統管理員帳戶產生的密碼。 以下是用於產生密碼的範例：

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault 的設計訴求是保護加密金鑰和其他祕密。 如需詳細資訊，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](./template-tutorial-use-key-vault.md)。 我們也建議您每三個月更新一次密碼。

## <a name="open-a-quickstart-template"></a>開啟快速入門範本

Azure 快速入門範本是 ARM 範本的存放庫。 您可以尋找範例範本並加以自訂，而不要從頭建立範本。 本教學課程中使用的範本名為[部署簡單的 Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/)。

1. 在 Visual Studio Code 中，選取 [檔案] > [開啟檔案]。
2. 在 [檔案名稱] 中，貼上下列 URL：

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. 選取 [開啟] 以開啟檔案。
4. 選取 [檔案] > [另存新檔]，以名稱 _azuredeploy.json_ 將檔案的複本儲存至您的本機電腦。

## <a name="explore-the-template"></a>瀏覽範本

當您探索這一節中的範本時，請試著回答下列問題：

* 此範本中定義了多少個 Azure 資源？
* 其中一項資源是 Azure 儲存體帳戶。 定義是否與最後一個教學課程中使用的定義相仿？
* 您是否可找到此範本中定義的資源所適用的範本參考？
* 您是否可找到資源的相依性？

1. 在 Visual Studio Code 中摺疊元素，直到您只看到`resources`內的第一層元素和第二層元素：

    ![Visual Studio Code ARM 範本](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    範本中定義了六項資源：

   * [**Microsoft.Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     在自訂範本之前，建議您先檢閱範本參考。

1. 展開第一項資源。 這是儲存體帳戶。 將資源定義與[範本參考](/azure/templates/Microsoft.Storage/storageAccounts)相比較。

    ![Visual Studio Code ARM 範本儲存體帳戶定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. 展開第二項資源。 資源類型為 `Microsoft.Network/publicIPAddresses`。 將資源定義與[範本參考](/azure/templates/microsoft.network/publicipaddresses)相比較。

    ![Visual Studio Code ARM 範本公用 IP 位址定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. 展開第三項資源。 資源類型為 `Microsoft.Network/networkSecurityGroups`。 將資源定義與[範本參考](/azure/templates/microsoft.network/networksecuritygroups)相比較。

    ![Visual Studio Code ARM 範本網路安全性群組定義](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. 展開第四項資源。 資源類型為 `Microsoft.Network/virtualNetworks`：

    ![Visual Studio Code ARM 範本虛擬網路 dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    `dependsOn` 元素可讓您定義一項資源，作為一或多項資源的相依項目。 此資源依存於另一項資源：

    * `Microsoft.Network/networkSecurityGroups`

1. 展開第五項資源。 資源類型為 `Microsoft.Network/networkInterfaces`。 此資源依存於其他兩項資源：

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. 展開第六項資源。 此資源是虛擬機器。 它依存於其他兩項資源：

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

下圖說明此範本的資源和相依性資訊：

![Visual Studio Code ARM 範本相依性圖表](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

藉由指定相依性，Resource Manager 將可有效部署解決方案。 它會以平行方式部署儲存體帳戶、公用 IP 位址和虛擬網路，因為它們沒有相依性。 在公用 IP 位址和虛擬網路部署之後，會建立網路介面。 當所有其他資源皆部署後，Resource Manager 會部署虛擬機器。

## <a name="deploy-the-template"></a>部署範本

1. 登入 [Azure Cloud Shell](https://shell.azure.com)

1. 藉由選取左上角的 **PowerShell** 或 **Bash** (適用於 CLI) 來選擇您慣用的環境。  切換時必須重新啟動殼層。

    ![Azure 入口網站的 Cloud Shell 上傳檔案](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. 選取 [上傳/下載檔案]，然後選取 [上傳]。 請參閱上一個螢幕擷取畫面。 選取您稍早儲存的檔案。 上傳檔案之後，您可以使用 `ls` 命令和 `cat` 命令來確認檔案是否已成功上傳。

1. 然後執行下列 PowerShell 指令碼來部署範本。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. 透過 RDP 連線至虛擬機器，以確認虛擬機器已成功建立。

## <a name="clean-up-resources"></a>清除資源

不再需要 Azure 資源時，可藉由刪除資源群組來清除您所部署的資源。

1. 在 Azure 入口網站中，選取左側功能表中的 [資源群組]。
2. 在 [依名稱篩選] 欄位中輸入資源群組名稱。
3. 選取資源群組名稱。 您在資源群組中應該會看到共計六個資源。
4. 從頂端功能表中選取 [刪除資源群組]。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已開發和部署用來建立虛擬機器、虛擬網路和相依資源的範本。 若要了解如何使用部署指令碼來執行部署前/後的作業，請參閱：

> [!div class="nextstepaction"]
> [使用部署指令碼](./template-tutorial-deployment-script.md)
