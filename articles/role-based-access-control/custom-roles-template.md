---
title: 使用 Azure Resource Manager 範本建立 Azure 自訂角色-Azure RBAC
description: 瞭解如何使用 Azure Resource Manager 範本 (ARM 範本來建立 Azure 自訂角色) 以及 (Azure RBAC) 使用 Azure 角色型存取控制。
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: 96dfdc0a1c32237c55d4e65bb25989656e2a4ad2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097017"
---
# <a name="create-an-azure-custom-role-using-an-arm-template"></a>使用 ARM 範本建立 Azure 自訂角色

如果 [Azure 內建角色](built-in-roles.md) 不符合您組織的特定需求，您可以建立自己的 [自訂角色](custom-roles.md)。 本文說明如何使用 Azure Resource Manager 範本 (ARM 範本) 來建立自訂角色。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

若要建立自訂角色，請指定角色名稱、許可權，以及可使用角色的位置。 在本文中，您會建立名為 _Custom role-RG Reader_ 的角色，其中包含可在訂用帳戶範圍或更低範圍指派的資源許可權。

如果您的環境符合必要條件，而且您很熟悉 ARM 範本，請選取 [部署至 Azure] 按鈕。 範本會在 Azure 入口網站中開啟。

[![部署至 Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsubscription-deployments%2Fcreate-role-def%2Fazuredeploy.json)

## <a name="prerequisites"></a>必要條件

若要建立自訂角色，您必須擁有：

- 建立自訂角色的許可權，例如 [擁有](built-in-roles.md#owner) 者或 [使用者存取系統管理員](built-in-roles.md#user-access-administrator)。

## <a name="review-the-template"></a>檢閱範本

本文中使用的範本是來自 [Azure 快速入門範本](https://azure.microsoft.com/resources/templates/create-role-def)。 範本有四個參數和一個 resources 區段。 這四個參數為：

- 具有預設值的動作陣列 `["Microsoft.Resources/subscriptions/resourceGroups/read"]` 。
- `notActions`具有空白預設值的陣列。
- 預設值為的角色名稱 `Custom Role - RG Reader` 。
- 預設值為的角色描述 `Subscription Level Deployment of a Role Definition` 。

可以指派此自訂角色的範圍會設定為目前的訂用帳戶。

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

範本中定義的資源為：

- [Microsoft. 授權/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

## <a name="deploy-the-template"></a>部署範本

遵循下列步驟來部署先前的範本。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 適用於 PowerShell 的 Azure Cloud Shell。

1. 請將下列指令碼複製並貼到 Cloud Shell。

    ```azurepowershell-interactive
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. 輸入部署的位置，例如 *centralus* 。

1. 將自訂角色的動作清單輸入為以逗號分隔的清單，例如 Microsoft .resources/ *resources/read、Microsoft .resources/訂閱/resourceGroups/read* 。

1. 如有需要，請按 Enter 鍵以執行 `New-AzDeployment` 命令。

    [Test-azdeployment](/powershell/module/az.resources/new-azdeployment)命令會部署範本以建立自訂角色。

    您應該會看到類似以下的輸出：

    ```azurepowershell-interactive
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

1. 執行 [>get-azroledefinition](/powershell/module/az.resources/get-azroledefinition) 命令來列出自訂角色。

    ```azurepowershell-interactive
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    您應該會看到如下輸出：

    ```azurepowershell-interactive
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

1. 在 Azure 入口網站中，開啟您的訂用帳戶。

1. 在左側功能表中，選取 [ **存取控制] (IAM)** 。

1. 選取 [ **角色** ] 索引標籤。

1. 將 [ **類型** ] 清單設定為 [ **CustomRole** ]。

1. 確認已列出 **自訂角色-RG 讀取** 者角色。

   ![Azure 入口網站中的新自訂角色](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>清除資源

若要移除自訂角色，請遵循下列步驟。

1. 執行下列命令以移除自訂角色。

    ```azurepowershell-interactive
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. 輸入 **Y** ，確認您想要移除自訂角色。

## <a name="next-steps"></a>下一步

- [瞭解 Azure 角色定義](role-definitions.md)
- [快速入門：使用 Azure Resource Manager 範本新增 Azure 角色指派](quickstart-role-assignments-template.md)
- [ARM 範本文件](../azure-resource-manager/templates/index.yml)
