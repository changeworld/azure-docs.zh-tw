---
title: 使用 Azure Resource Manager 範本建立 Azure 自訂角色-Azure RBAC
description: 瞭解如何使用 Azure Resource Manager 範本和 Azure 角色型存取控制（Azure RBAC）來建立 Azure 自訂角色。
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85397938"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本建立 Azure 自訂角色

如果[Azure 內建角色](built-in-roles.md)不符合您組織的特定需求，您可以建立自己的[自訂角色](custom-roles.md)。 本文說明如何使用 Azure Resource Manager 範本建立自訂角色。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>必要條件

若要建立自訂角色，您必須具有：

- 建立自訂角色的權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取管理員](built-in-roles.md#user-access-administrator)

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，您可以指定角色名稱、許可權，以及可使用角色的位置。 在本文中，您會建立名為「自訂角色-RG 讀取器」的角色，其中包含可在訂用帳戶範圍或更低層級指派的資源許可權。

### <a name="review-the-template"></a>檢閱範本

本文中使用的範本是來自[Azure 快速入門範本](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)。 此範本具有四個參數和 resources 區段。 這四個參數為：

- 預設值為 ["Microsoft. Resources/訂用帳戶/resourceGroups/read"] 的動作陣列
- 具有空白預設值的 notActions 陣列
- 預設值為「自訂角色-RG 讀取器」的角色名稱
- 預設值為「角色定義的訂用帳戶層級部署」的角色描述

範本中定義的資源為：

- [Microsoft 授權/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

可以指派此自訂角色的範圍會設定為目前的訂用帳戶。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>部署範本

請遵循下列步驟來部署先前的範本。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 適用於 PowerShell 的 Azure Cloud Shell。

1. 請將下列指令碼複製並貼到 Cloud Shell。

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. 輸入部署的位置，例如*centralus*。

1. 將自訂角色的動作清單輸入為以逗號分隔的清單，例如 Microsoft .resources/ *resources/read、Microsoft .resources/訂用帳戶/resourceGroups/read*。

1. 如有需要，請按 Enter 鍵執行 New-azdeployment 命令。

    [New-azdeployment](/powershell/module/az.resources/new-azdeployment)命令會部署範本來建立自訂角色。

    您應該會看到類似以下的輸出：

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>檢閱已部署的資源

請遵循下列步驟來確認已建立自訂角色。

1. 執行[get-azroledefinition](/powershell/module/az.resources/get-azroledefinition)命令以列出自訂角色。

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    您應該會看到類似以下的輸出：

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. 在 [Azure 入口網站中，開啟您的訂用帳戶。

1. 在左側功能表中，按一下 [存取控制 (IAM)]。

1. 按一下 [**角色**] 索引標籤。

1. 將 [**類型**] 清單設定為 [ **CustomRole**]。

1. 確認已列出 [**自訂角色-RG 讀取**者] 角色。

   ![Azure 入口網站中的新自訂角色](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>清除資源

若要移除自訂角色，請遵循下列步驟。

1. 執行下列命令來移除自訂角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. 輸入**Y** ，確認您想要移除自訂角色。

## <a name="next-steps"></a>後續步驟

- [瞭解 Azure 角色定義](role-definitions.md)
- [快速入門：使用 Azure Resource Manager 範本新增 Azure 角色指派](quickstart-role-assignments-template.md)
- [ARM 範本文件](../azure-resource-manager/templates/index.yml)
