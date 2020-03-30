---
title: 使用 Azure 資源管理器部署工作室（經典）工作區
titleSuffix: ML Studio (classic) - Azure
description: 如何使用 Azure 資源管理器範本為 Azure 機器學習工作室（經典）部署工作區
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218122"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>使用 Azure 資源管理器部署 Azure 機器學習工作室（經典）工作區

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

使用 Azure Resource Manager 部署範本提供了可擴充的方式來部署具有驗證和重試機制的互連元件，為您節省時間。 例如，要設置 Azure 機器學習工作室（經典）工作區，需要先配置 Azure 存儲帳戶，然後部署工作區。 假想您要對數百個工作區手動進行此動作。 更簡單的替代方法是使用 Azure 資源管理器範本來部署 Studio（經典）工作區及其所有依賴項。 這篇文章會帶領您逐步完成此程序。 如需 Azure Resource Manager 的詳細概觀，請參閱 [Azure Resource Manager 概觀](../../azure-resource-manager/management/overview.md)。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>逐步說明：建立 Machine Learning 工作區
我們將創建 Azure 資源組，然後使用資源管理器範本部署新的 Azure 存儲帳戶和新的 Azure 機器學習工作室（經典）工作區。 部署完成之後，我們會印出所建立的工作區的重要資訊 (主索引鍵、工作區識別碼和工作區的 URL)。

### <a name="create-an-azure-resource-manager-template"></a>建立 Azure Resource Manager 範本

Machine Learning 工作區需有 Azure 儲存體帳戶來儲存連結到它的資料集。
以下範本會使用資源群組的名稱來產生儲存體帳戶名稱和工作區名稱。  建立工作區時，它也會使用儲存體帳戶名稱做為屬性。

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
將此範本在 c:\temp\ 下儲存為 mlworkspace.json 檔案。

### <a name="deploy-the-resource-group-based-on-the-template"></a>依據範本部署資源群組

* 開啟 PowerShell
* 安裝 Azure Resource Manager 和 Azure 服務管理的模組

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   下列步驟會下載並安裝完成剩餘步驟所需的模組。 這只需要在您執行 PowerShell 命令的環境中執行一次。

* 向 Azure 驗證

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
需要為每個工作階段重複執行這個步驟。 驗證之後，應該會顯示您的訂用帳戶資訊。

![Azure 帳戶](./media/deploy-with-resource-manager-template/azuresubscription.png)

現在，我們已經可以存取 Azure，便可以建立資源群組。

* 建立資源群組

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

請確認資源群組已正確佈建。 **預配狀態**應為"成功"。
範本會使用資源群組名稱來產生儲存體帳戶名稱。 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能使用數字和小寫字母。

![資源群組](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* 使用資源群組部署，部署新的 Machine Learning 工作區。

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

一旦完成部署之後，就可以直接存取您所部署的工作區的屬性。 例如，您可以存取主要金鑰權杖。

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

檢索現有工作區權杖的另一種方法是使用 Invoke-AzResourceAction 命令。 例如，您可以列出所有工作區的主要和次要權杖。

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
預配工作區後，還可以使用[Azure 機器學習工作室的 PowerShell 模組（經典）](https://aka.ms/amlps)自動執行許多 Azure 機器學習工作室（經典）任務。

## <a name="next-steps"></a>後續步驟

* 深入了解 [編寫 Azure Resource Manager 範本](../../azure-resource-manager/templates/template-syntax.md)。
* 看看 [Azure 快速入門範本儲存機制](https://github.com/Azure/azure-quickstart-templates)。
* 觀看這段 [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39)影片。
* 請參閱 [Resource Manager 範本參考說明](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->
