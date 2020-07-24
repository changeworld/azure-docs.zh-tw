---
title: 使用 PowerShell 和範本部署資源
description: 使用 Azure Resource Manager 和 Azure PowerShell，將資源部署至 Azure。 資源會定義在 Resource Manager 範本中。
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: 64993b526b67430266a8b3e85e3bcc233a3e28a3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079514"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>使用 ARM 範本與 Azure PowerShell 來部署資源

本文說明如何使用 Azure PowerShell 搭配 Azure Resource Manager 範本（ARM 範本）將您的資源部署至 Azure。 如果您不熟悉部署和管理 Azure 解決方案的概念，請參閱[範本部署總覽](overview.md)。

## <a name="deployment-scope"></a>部署範圍

您可以將部署的目標設為資源群組、訂用帳戶、管理群組或租使用者。 在大部分情況下，您會將部署目標設為資源群組。 若要在較大的範圍內套用原則和角色指派，請使用訂用帳戶、管理群組或租使用者部署。 部署至訂用帳戶時，您可以建立資源群組，並將資源部署到其中。

視部署的範圍而定，您可以使用不同的命令。

* 若要部署至**資源群組**，請使用[new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)：

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* 若要部署至**訂**用帳戶，請使用 AzSubscriptionDeployment：

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  如需訂用帳戶層級部署的詳細資訊，請參閱[在訂用帳戶層級建立資源群組和資源](deploy-to-subscription.md)。

* 若要部署至**管理群組**，請使用[AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment)。

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  如需管理群組層級部署的詳細資訊，請參閱[在管理群組層級建立資源](deploy-to-management-group.md)。

* 若要部署至**租**使用者，請使用[AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment)。

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  如需租用戶層級部署的詳細資訊，請參閱[在租用戶層級建立資源](deploy-to-tenant.md)。

本文中的範例會使用資源群組部署。

## <a name="prerequisites"></a>先決條件

您需要一個要部署的範本。 如果您還沒有帳戶，請從 Azure 快速入門範本存放庫下載並儲存[範例範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 本文所使用的本機檔案名稱是 **c:\MyTemplates\azuredeploy.json**。

除非您使用 Azure Cloud Shell 來部署範本，否則您需要安裝 Azure PowerShell 並聯機到 Azure：

- **在本機電腦上安裝 Azure PowerShell Cmdlet。** 如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** 來連線至 Azure。 如果您有多個 Azure 訂用帳戶，則可能還需要執行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 如需詳細資訊，請參閱[使用多個 Azure 訂用帳戶](/powershell/azure/manage-subscriptions-azureps)。

## <a name="deploy-local-template"></a>部署本機範本

下列範例會建立資源群組，並從您的本機電腦部署範本。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

部署需要幾分鐘的時間才能完成。

## <a name="deployment-name"></a>部署名稱

在上述範例中，您已將部署命名為 `ExampleDeployment` 。 如果您未提供部署的名稱，則會使用範本檔案的名稱。 例如，如果您部署名為的範本， `azuredeploy.json` 但未指定部署名稱，則會將部署命名為 `azuredeploy` 。

每次執行部署時，會使用部署名稱將專案新增至資源群組的部署歷程記錄。 如果您執行另一個部署並指定相同的名稱，則會將先前的專案取代為目前的部署。 如果您想要在部署歷程記錄中維護唯一的專案，請為每個部署提供一個唯一的名稱。

若要建立唯一的名稱，您可以指派一個亂數字。

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

或者，加入日期值。

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

如果您使用相同的部署名稱對相同的資源群組執行並行部署，則只會完成最後一個部署。 任何名稱不會完成的部署都會取代為最後一個部署。 例如，如果您執行名為的部署， `newStorage` 並部署名為的儲存體帳戶 `storage1` ，而且同時執行名為的另一個部署，並部署 `newStorage` 名為的儲存體帳戶 `storage2` ，則您只會部署一個儲存體帳戶。 產生的儲存體帳戶名稱為 `storage2` 。

不過，如果您執行名為 `newStorage` 的部署來部署名為的儲存體帳戶 `storage1` ，而且在它完成後立即執行另一個名為的部署，並部署 `newStorage` 名為的儲存體帳戶 `storage2` ，則您有兩個儲存體帳戶。 其中一個名為 `storage1` ，另一個名為 `storage2` 。 但是，在部署歷程記錄中，您只會有一個專案。

當您為每個部署指定唯一的名稱時，您可以同時執行它們，而不會發生衝突。 如果您執行名為的部署， `newStorage1` 並部署名為的儲存體帳戶 `storage1` ，而且同時執行名為的另一個部署 `newStorage2` 來部署名為的儲存體帳戶 `storage2` ，則您在部署歷程記錄中會有兩個儲存體帳戶和兩個專案。

若要避免與並行部署發生衝突，並確保部署歷程記錄中的唯一專案，請為每個部署提供唯一的名稱。

## <a name="deploy-remote-template"></a>部署遠端範本

您可能會想要將 ARM 範本儲存在外部位置，而不是將它儲存在本機電腦上。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，以在組織內共用存取。

若要部署外部範本，請使用**TemplateUri**參數。 在範例中使用 URI 以部署來自 GitHub 的範例範本。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。 若要進行教學課程，請參閱[教學課程：在 ARM 範本部署中整合 Azure Key Vault](template-tutorial-use-key-vault.md)。

## <a name="preview-changes"></a>預覽變更

在部署您的範本之前，您可以預覽範本對環境所做的變更。 使用「[假設](template-deploy-what-if.md)」作業來確認範本會進行您預期的變更。 假設也會驗證範本是否有錯誤。

## <a name="deploy-from-azure-cloud-shell"></a>從 Azure Cloud Shell 部署

您可以使用 [Azure Cloud Shell](https://shell.azure.com) 來部署範本。 若要部署外部範本，請提供範本的 URI。 若要部署本機範本，您必須先將範本載入 Cloud Shell 的儲存體帳戶。 若要將檔案上傳到殼層，請從殼層視窗選取 [上傳/下載檔案]**** 功能表圖示。

若要開啟 Cloud Shell，請流覽至 [https://shell.azure.com](https://shell.azure.com) ，或從下列程式碼區段選取 [**試試看**]：

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

若要將程式碼貼到殼層中，請在殼層內按一下滑鼠右鍵，然後選取 [貼上]****。

## <a name="pass-parameter-values"></a>傳遞參數值

若要傳遞參數值，您可以使用內嵌參數或參數檔案。

### <a name="inline-parameters"></a>內嵌參數

若要傳遞內嵌參數，請使用 `New-AzResourceGroupDeployment` 命令提供參數名稱。 例如，若要將字串和陣列傳遞至範本，請使用：

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

您也可以取得檔案內容，並提供該內容作為內嵌參數。

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

當您需要提供組態值時，從檔案取得參數值會很有幫助。 例如，您可以提供 [Linux 虛擬機器的 cloud-init 值](../../virtual-machines/linux/using-cloud-init.md)。

如果您需要傳入物件的陣列，請在 PowerShell 中建立雜湊表，並將它們新增至陣列。 在部署期間傳遞該陣列做為參數。

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>參數檔案

相對於在您的指令碼中將參數做為內嵌值傳遞，使用包含該參數值的 JSON 檔案可能較為容易。 參數檔案可以是本機檔案或具有可存取 URI 的外部檔案。

如需參數檔案的詳細資訊，請參閱[建立 Resource Manager 參數檔案](parameter-files.md)。

若要傳遞本機參數檔案，請使用 **TemplateParameterFile** 參數︰

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

若要傳遞外部參數檔案，請使用 **TemplateParameterUri** 參數︰

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>後續步驟

- 在您收到錯誤時，若要回復為成功的部署，請參閱[錯誤回復至成功部署](rollback-on-error.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 若要瞭解如何在您的範本中定義參數，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
