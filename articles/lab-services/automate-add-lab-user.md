---
title: 在 Azure 開發人員測試實驗室中自動添加實驗室使用者 |微軟文件
description: 本文演示如何使用 Azure 資源管理器範本、PowerShell 和 CLI 自動將使用者添加到 Azure 開發人員測試實驗室中的實驗室。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023617"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>自動將實驗室使用者新增到 Azure 開發人員測試實驗室中的實驗室
Azure 開發人員測試實驗室允許您使用 Azure 門戶快速創建自助服務開發測試環境。 但是,如果您有多個團隊和多個 DevTest Labs 實例,則自動化創建過程可以節省時間。 [Azure 資源管理員範本](https://github.com/Azure/azure-devtestlab/tree/master/Environments)允許您創建實驗室、實驗室 VM、自定義映射、公式,並自動添加使用者。 本文特別側重於將使用者添加到 DevTest Labs 實例。

要將使用者添加到實驗室,您需要將該使用者添加到實驗室的**DevTest Labs 使用者**角色。 本文展示如何使用以下方法之一自動將使用者添加到實驗室:

- Azure 資源管理員範本
- Azure PowerShell Cmdlet 
- Azure CLI。

## <a name="use-azure-resource-manager-templates"></a>使用 Azure 資源管理員範本
以下範例資源管理員範本指定要添加到實驗室的**DevTest Labs 使用者**角色的使用者。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

如果要在創建實驗室的同一範本中分配角色,請記住在角色分配資源和實驗室之間添加依賴項。 有關詳細資訊,請參閱在[Azure 資源管理員樣本一文中定義相依項 。](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>角色配置資源資訊
角色分配資源需要指定類型和名稱。

需要注意的第一件事是,資源的類型與資源組的類型不`Microsoft.Authorization/roleAssignments`相同。  相反,資源類型遵循模式`{provider-namespace}/{resource-type}/providers/roleAssignments`。 在這種情況下,資源類型將為`Microsoft.DevTestLab/labs/providers/roleAssignments`。

角色分配名稱本身需要全域唯一。  賦值的名稱使用模式`{labName}/Microsoft.Authorization/{newGuid}`。 `newGuid`是範本的參數值。 它確保角色分配名稱是唯一的。 由於沒有用於創建 GUID 的樣本函數,因此您需要使用任何 GUID 生成器工具自行生成 GUID。  

在範本中,角色分配的名稱由`fullDevTestLabUserRoleName`變數定義。 樣本的確切行是:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>角色配置資源屬性
角色分配本身定義三個屬性。 它需要`roleDefinitionId`與`principalId` `scope` 。

### <a name="role-definition"></a>角色定義
角色定義識別碼是現有角色定義的字串識別碼。 角色 ID 在`/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`窗體 中。 

訂閱 ID`subscription().subscriptionId`是使用 範本函數獲取的。  

您需要獲取`DevTest Labs User`內建角色的角色定義。 要取得[開發人員測試實驗室使用者](../role-based-access-control/built-in-roles.md#devtest-labs-user)角色的 GUID,可以使用[角色分配 REST API](/rest/api/authorization/roleassignments)或[獲取 ARole 定義](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)cmdlet。

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

角色識別碼在變數部份中定義,並命名為`devTestLabUserRoleId`。 在範本中,角色 ID 設置為:111111111-0000-0000-1111111111111111111111。 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>主體識別碼
主體 ID 是要作為實驗室使用者添加到實驗室的使用者、組或服務主體的對象 ID。 範本使用`ObjectId`作為參數。

您可以使用[獲取 AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0)、[獲取 AzureRMADGroup 或[取得 AzureRMAD 服務主](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0)電源 Shell cmdlet 獲得 ObjectId。 這些 cmdlet 傳回具有 ID 屬性(即您需要的物件 ID)的單個或活動目錄物件的清單。 下面的範例展示如何獲取公司中單個使用者的物件 ID。

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

您還可以使用 Azure 活動目錄 PowerShell cmdlet,其中包括[取得 MsolUser、Get-MsolGroup](/powershell/module/msonline/get-msoluser?view=azureadps-1.0)和[Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)[Get-MsolService 原則](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0)。

### <a name="scope"></a>影響範圍
範圍指定角色分配應為其應用的資源或資源組。 對於資源,作用域以: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`。 `subscription().subscriptionId`範本使用函數`subscription-id`填充零件`resourceGroup().name`和 範本函數來`resource-group-name`填充 零件。 使用這些函數意味著要為其分配角色的實驗室必須存在於當前訂閱中,並且與範本部署到的同一資源組中存在。 最後一`resource-name`部分, 是實驗室的名稱。 此值通過此示例中的範本參數接收。 

樣本中的角色範圍: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>部署範本
首先,創建一個參數檔(例如:azuredeploy.parameters.json),該檔傳遞資源管理器範本中參數的值。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

然後,使用[新 AzureRmResourceResourceResourceGroup 部署](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0)電源 Shell cmdlet 部署資源管理器範本。 以下範例命令將人員、組或服務主體分配給實驗室的 DevTest Labs 使用者角色。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

請務必注意,組部署名稱和角色分配 GUID 需要是唯一的。 如果嘗試使用非唯一 GUID 部署資源分配,則`RoleAssignmentUpdateNotPermitted`將出現錯誤。

如果計劃多次使用該範本將多個活動目錄物件添加到實驗室的 DevTest Labs 使用者角色,請考慮在 PowerShell 命令中使用動態物件。 下面的範例使用[New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet 動態指定資源組部署名稱和角色分配 GUID。

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>使用 Azure PowerShell
如簡介中所述,您將創建新的 Azure 角色分配,以將使用者添加到實驗室的**DevTest Labs 使用者**角色。 在 PowerShell 中,透過使用[「新建 Azure 皇家分配](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0)」cmdlet 執行此操作。 此 cmdlet 具有許多可選參數,允許靈活性。 可以`ObjectId`指定`SigninName`為`ServicePrincipalName`授予許可權的物件。  

下面是一個示例 Azure PowerShell 命令,該命令將使用者添加到指定實驗室中的 DevTest Labs 使用者角色。

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

指定授予權限的`ResourceName`資源 可以由`ResourceType`的合併`ResourceGroup`指定 ,`scope`或 參數。 無論使用參數組合如何,都向 cmdlet 提供足夠的資訊,以唯一標識 Active Directory 物件(使用者、組或服務主體)、作用域(資源組或資源)和角色定義。

## <a name="use-azure-command-line-interface-cli"></a>使用 Azure 指令列介面 (CLI)
在 Azure CLI 中`az role assignment create`,使用命令將實驗室使用者添加到實驗室。 有關 Azure CLI cmdlet 的詳細資訊,請參閱[使用 RBAC 和 Azure CLI 管理對 Azure 資源的存取](../role-based-access-control/role-assignments-cli.md)。

授予訪問許可權的物件可以由`objectId``signInName`、`spn`參數指定。 授予對象訪問許可權的實驗室`scope`可以通過`resource-name``resource-type``resource-group`url 或和 參數的組合進行標識。

以下 Azure CLI 範例展示如何將人員添加到指定實驗室的 DevTest Labs 使用者角色。  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>後續步驟
查看下列文章：

- [使用 Azure CLI 在 DevTest Labs 中建立和管理虛擬機器](devtest-lab-vmcli.md)
- [使用 Azure PowerShell 使用開發人員測試實驗室建立虛擬機器](devtest-lab-vm-powershell.md)
- [使用命令列工具啟動與停止 Azure 開發人員測試實驗室虛擬機器](use-command-line-start-stop-virtual-machines.md)

