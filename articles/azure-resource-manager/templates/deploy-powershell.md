---
title: 使用 PowerShell 和範本部署資源
description: 使用 Azure 資源管理器和 Azure PowerShell 將資源部署到 Azure。 資源會定義在 Resource Manager 範本中。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153262"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>使用 ARM 範本和 Azure PowerShell 部署資源

瞭解如何將 Azure PowerShell 與 Azure 資源管理器 （ARM） 範本一起部署到 Azure。 有關部署和管理 Azure 解決方案的概念的詳細資訊，請參閱[範本部署概述](overview.md)。

## <a name="deployment-scope"></a>部署範圍

您可以將部署定位到資源組、訂閱組、管理組或租戶。 在大多數情況下，您將將部署目標鎖定到資源組。 要在更大的範圍內應用策略和角色指派，請使用訂閱、管理組或租戶部署。 部署到訂閱時，可以創建資源組並將資源部署到該訂閱。

根據部署的範圍，使用不同的命令。

要部署到**資源組**，請使用[New-AzResourceGroup 部署](/powershell/module/az.resources/new-azresourcegroupdeployment)：

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

要部署到**訂閱**，請使用"新訂閱部署"：

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

有關訂閱級別部署的詳細資訊，請參閱[在訂閱級別創建資源組和資源](deploy-to-subscription.md)。

要部署到**管理組**，請使用[New-AzManagementGroup 部署](/powershell/module/az.resources/New-AzManagementGroupDeployment)。

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

有關管理組級別部署的詳細資訊，請參閱[在管理組級別創建資源](deploy-to-management-group.md)。

要部署到**租戶**，請使用[新阿茲租戶部署](/powershell/module/az.resources/new-aztenantdeployment)。

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

有關租戶級別部署的詳細資訊，請參閱[在租戶級別創建資源](deploy-to-tenant.md)。

本文中的示例使用資源組部署。

## <a name="prerequisites"></a>Prerequisites

您需要部署一個範本。 如果還沒有，請從 Azure 快速入門範本回購中下載並保存[示例範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json)。 本文所使用的本機檔案名稱是 **c:\MyTemplates\azuredeploy.json**。

除非使用 Azure 雲外殼部署範本，否則需要安裝 Azure PowerShell 並連接到 Azure：

- **在本機電腦上安裝 Azure PowerShell Cmdlet。** 如需詳細資訊，請參閱[開始使用 Azure PowerShell](/powershell/azure/get-started-azureps)。
- **使用 [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)** 來連線至 Azure。 如果您有多個 Azure 訂用帳戶，則可能還需要執行 [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext)。 如需詳細資訊，請參閱[使用多個 Azure 訂用帳戶](/powershell/azure/manage-subscriptions-azureps)。

## <a name="deploy-local-template"></a>部署本機範本

下面的示例創建一個資源組，並從本地電腦部署範本。 資源群組的名稱只能包含英數字元、句點 (.)、底線、連字號及括弧。 最多可有 90 個字元。 不能以句點結束。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

部署需要幾分鐘的時間才能完成。

## <a name="deploy-remote-template"></a>部署遠端範本

您可能希望將 ARM 範本存儲在本地電腦上，而不是將 ARM 範本存儲在本地電腦上。 您可以將範本儲存在原始檔控制存放庫 (例如 GitHub) 中。 或者，您可以將它們儲存在 Azure 儲存體帳戶中，以在組織內共用存取。

要部署外部範本，請使用**範本Uri**參數。 在範例中使用 URI 以部署來自 GitHub 的範例範本。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

上述範例針對範本需要可公開存取 URI，這適用於大部分的案例，因為您的範本不應該包含機密資料。 如果您需要指定機密資料 (例如系統管理員密碼)，請將該值以安全參數傳遞。 不過，如果不希望將範本公開存取，您可以將它儲存在私人儲存體容器中加以保護。 如需部署需要共用存取簽章 (SAS) 權杖之範本的相關資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。 要流覽教程，請參閱[教程：在 ARM 範本部署中集成 Azure 金鑰保存庫](template-tutorial-use-key-vault.md)。

## <a name="deploy-from-azure-cloud-shell"></a>從 Azure 雲外殼部署

您可以使用 [Azure Cloud Shell](https://shell.azure.com) 來部署範本。 若要部署外部範本，請提供範本的 URI。 若要部署本機範本，您必須先將範本載入 Cloud Shell 的儲存體帳戶。 若要將檔案上傳到殼層，請從殼層視窗選取 [上傳/下載檔案]**** 功能表圖示。

要打開雲外殼，請流覽到[https://shell.azure.com](https://shell.azure.com)，或從以下代碼部分選擇 **"試用"：**

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

如果需要傳遞物件陣列，請在 PowerShell 中創建雜湊表並將其添加到陣列中。 在部署期間將該陣列作為參數傳遞。

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

有關參數檔的詳細資訊，請參閱[創建資源管理器參數檔](parameter-files.md)。

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

## <a name="test-template-deployments"></a>測試範本部署

要在不實際部署任何資源的情況下測試範本和參數值，請使用[測試-AzResourceGroup 部署](/powershell/module/az.resources/test-azresourcegroupdeployment)。 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

如果未偵測到錯誤，命令即完成，不會有回應。 如果偵測到錯誤，命令會傳回錯誤訊息。 例如，針對儲存體帳戶 SKU 傳遞不正確的值，則會傳回下列錯誤：

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

如果您的範本有語法錯誤，命令會傳回錯誤，指出無法剖析範本。 訊息會指出剖析錯誤的行號和位置。

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>後續步驟

- 要在收到錯誤時回滾到成功部署，請參閱[在錯誤時回滾到成功部署](rollback-on-error.md)。
- 若要指定如何處理存在於資源群組中、但尚未定義於範本中的資源，請參閱 [Azure Resource Manager 部署模式](deployment-modes.md)。
- 要瞭解如何在範本中定義參數，請參閱[瞭解 ARM 範本的結構和語法](template-syntax.md)。
- 如需部署需要 SAS 權杖之範本的詳細資訊，請參閱[使用 SAS 權杖部署私人範本](secure-template-with-sas-token.md)。
