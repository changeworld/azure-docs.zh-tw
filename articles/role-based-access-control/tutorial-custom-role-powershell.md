---
title: 教學課程：使用 Azure PowerShell 為 Azure 資源建立自訂角色
description: 在本教學課程中，使用 Azure PowerShell 開始為 Azure 資源建立自訂角色。
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.openlocfilehash: d337e31f554c0aabbb94771aa7bfca4afb19a431
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138270"
---
# <a name="tutorial-create-a-custom-role-for-azure-resources-using-azure-powershell"></a>教學課程：使用 Azure PowerShell 為 Azure 資源建立自訂角色

如果[適用於 Azure 資源的內建角色](built-in-roles.md)無法滿足您組織的特定需求，您可以建立自己的自訂角色。 在此教學課程中，您會使用 Azure PowerShell 建立名為讀者支援票證的自訂角色。 自訂角色可讓使用者檢視訂用帳戶的平面管理中的一切，也可開啟支援票證。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 建立自訂角色
> * 列出自訂角色
> * 更新自訂角色
> * 刪除自訂角色

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，您將會需要：

- 建立自訂角色的權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取管理員](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) 或 [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="sign-in-to-azure-powershell"></a>登入 Azure PowerShell

登入 [Azure PowerShell](/powershell/azure/authenticate-azureps)。

## <a name="create-a-custom-role"></a>建立自訂角色

建立自訂角色的最簡單方法就是從內建角色著手，加以編輯，然後建立新的角色。

1. 在 PowerShell 中，使用 [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) 命令，取得 Microsoft.Support 資源提供者的作業清單。 最好先了解可用來建立權限的作業。 您可以在 [Azure Resource Manager 資源提供者作業](resource-provider-operations.md#microsoftsupport)看見所有作業的清單。

    ```azurepowershell
    Get-AzProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. 使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令來輸出 JSON 格式的[讀者](built-in-roles.md#reader)角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. 在編輯器中開啟 **ReaderSupportRole.json** 檔案。

    下圖顯示了 JSON 輸出。 如需不同屬性的相關資訊，請參閱[自訂角色](custom-roles.md)。

    ```json
    {
      "Name": "Reader",
      "Id": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
      "IsCustom": false,
      "Description": "Lets you view everything, but not make any changes.",
      "Actions": [
        "*/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/"
      ]
    }
    ```
    
1. 編輯 JSON 檔案，以將 `"Microsoft.Support/*"` 作業新增至 `Actions` 屬性。 請務必在讀取作業之後包含逗號。 這個動作將允許使用者建立支援票證。

1. 使用 [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) 命令取得訂用帳戶的識別碼。

    ```azurepowershell
    Get-AzSubscription
    ```

1. 在 `AssignableScopes` 中，新增格式如下的訂用帳戶識別碼：`"/subscriptions/00000000-0000-0000-0000-000000000000"`

    您必須新增明確的訂用帳戶識別碼，否則無法將角色匯入您的訂用帳戶。

1. 刪除 `Id` 屬性行並將 `IsCustom` 屬性變更為 `true`。

1. 將 `Name` 和 `Description` 屬性變更為 [讀者支援票證] 和 [檢視訂用帳戶中的所有資訊，包括開啟的支援票證]。

    JSON 檔案看起來應該如下所示：

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. 若要建立新的自訂角色，請使用 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 命令並指定 JSON 角色定義檔案。

    ```azurepowershell
    New-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    新的自訂角色現在可於 Azure 入口網站中使用，而且可以指派給使用者、群組或服務主體 (就像內建角色一樣)。

## <a name="list-custom-roles"></a>列出自訂角色

- 若要列出所有的自訂角色，請使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令。

    ```azurepowershell
    Get-AzRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    您也可以在 Azure 入口網站中看見自訂角色。

    ![匯入 Azure 入口網站之自訂角色的螢幕擷取畫面](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>更新自訂角色

若要更新自訂角色，您可以更新 JSON 檔案，或使用 `PSRoleDefinition` 物件。

1. 若要更新 JSON 檔案，請使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令來輸出 JSON 格式的自訂角色。

    ```azurepowershell
    Get-AzRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. 在編輯器中開啟檔案。

1. 在 `Actions` 中，新增用以建立和管理資源群組部署 `"Microsoft.Resources/deployments/*"` 的作業。

    更新號的 JSON 檔案看起來應該如下所示：

    ```json
    {
      "Name": "Reader Support Tickets",
      "Id": "22222222-2222-2222-2222-222222222222",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. 若要更新自訂檔案，請使用 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 命令並指定已更新的 JSON 檔案。

    ```azurepowershell
    Set-AzRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. 若要使用 `PSRoleDefintion` 物件來更新自訂角色，首先使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令來取得角色。

    ```azurepowershell
    $role = Get-AzRoleDefinition "Reader Support Tickets"
    ```
    
1. 呼叫 `Add` 方法來新增用以讀取診斷設定的作業。

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. 使用 [Set-AzRoleDefinition](/powershell/module/az.resources/set-azroledefinition) 來更新角色。

    ```azurepowershell
    Set-AzRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>刪除自訂角色

1. 使用 [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) 命令來取得自訂角色的識別碼。

    ```azurepowershell
    Get-AzRoleDefinition "Reader Support Tickets"
    ```

1. 使用 [Remove-AzRoleDefinition](/powershell/module/az.resources/remove-azroledefinition) 命令，並指定要刪除自訂角色的角色識別碼。

    ```azurepowershell
    Remove-AzRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. 當系統要求您確認時，請輸入 **Y**。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Azure PowerShell 建立適用於 Azure 資源的自訂角色](custom-roles-powershell.md)
