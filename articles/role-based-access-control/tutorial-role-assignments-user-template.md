---
title: 教學課程：使用 RBAC 和 Resource Manager 範本為使用者授與 Azure 資源的存取權
description: 在本教學課程中，了解如何使用角色型存取控制 (RBAC) 和 Azure Resource Manager 範本為使用者授與 Azure 資源的存取權。
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: 96ca4f65d2def5f5004388c533410f09cc2a71fa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138208"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>教學課程：使用 RBAC 和 Resource Manager 範本為使用者授與 Azure 資源的存取權

[角色型存取控制 (RBAC)](overview.md) 是您管理對 Azure 資源存取的機制。 在本教學課程中，您會建立資源群組並對使用者授與存取權，讓其在資源群組中建立和管理虛擬機器。 本教學課程著重於部署 Resource Manager 範本來授與存取權的程序。 如需開發 Resource Manager 範本的詳細資訊，請參閱 [Resource Manager 文件](/azure/azure-resource-manager/)和[範本參考](/azure/templates/microsoft.authorization/allversions
)。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 對使用者授與資源群組範圍的存取權
> * 驗證部署
> * 清除

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要新增和移除角色指派，您必須具有：

* `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)

## <a name="grant-access"></a>授與存取權

本快速入門中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/)。 在[這裡](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization)可以找到更多 Azure 授權相關範本。

若要部署範本，請選取 [試試看]  以開啟 Azure Cloud Shell，然後將下列 PowerShell 指令碼貼到 Shell 視窗中。 若要貼上程式碼，請以滑鼠右鍵按一下 Shell 視窗，然後選取 [貼上]  。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>驗證部署

1. 登入 [Azure 入口網站](https://portal.azure.com)。
1. 開啟您在最後一個程序中建立的資源群組。 預設名稱是附加 **rg** 的專案名稱。
1. 從左側功能表中選取 [存取控制 (IAM)]  。
1. 選取 [角色指派]  。 
1. 在 [名稱]  中，輸入您在最後一個程序中輸入的電子郵件地址。 您應會看到具有此電子郵件地址的使用者具有 [虛擬機器參與者]  角色。

## <a name="clean-up"></a>清除

若要移除在最後一個程序中建立的資源群組，請選取 [試試看]  以開啟 Azure Cloud Shell，然後將下列 PowerShell 指令碼貼到 Shell 視窗中。

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [教學課程：使用 RBAC 與 Azure PowerShell 為使用者授與 Azure 資源的存取權](tutorial-role-assignments-user-powershell.md)