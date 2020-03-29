---
title: 僅使用 Azure Cosmos DB 限制使用者訪問資料操作
description: 瞭解如何僅使用 Azure Cosmos DB 限制對資料操作的訪問
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980370"
---
# <a name="restrict-user-access-to-data-operations-only"></a>限制使用者只能存取資料作業

在 Azure Cosmos DB 中，有兩種方法可以驗證與資料庫服務的交互：
- 在與 Azure 門戶交互時，請使用 Azure 活動目錄標識，
- 從 API 和 SDK 發出調用時，使用 Azure Cosmos DB[金鑰](secure-access-to-data.md#master-keys)或[資源權杖](secure-access-to-data.md#resource-tokens)。

每個身份驗證方法都允許訪問不同的操作集，有些重疊：![每個身份驗證類型拆分操作](./media/how-to-restrict-user-data/operations.png)

在某些情況下，您可能希望限制組織的某些使用者僅執行資料操作（即 CRUD 請求和查詢）。 對於不需要創建或刪除資源的開發人員，或更改他們正在處理的容器的預配輸送量，通常就是這種情況。

您可以通過應用以下步驟來限制訪問：
1. 為要限制存取權限的使用者創建自訂 Azure 活動目錄角色。 自訂活動目錄角色應具有使用 Azure Cosmos DB[的細微性操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)對操作的細細微性存取層級。
1. 禁止使用金鑰執行非資料操作。 您可以通過僅將這些操作限制為 Azure 資源管理器調用來實現此目的。

本文的下一節演示如何執行這些步驟。

> [!NOTE]
> 為了在後續部分中執行這些命令，您需要安裝 Azure PowerShell 模組 3.0.0 或更高版本，以及嘗試修改的訂閱上的[Azure 擁有者角色](../role-based-access-control/built-in-roles.md#owner)。

在下一節中的 PowerShell 腳本中，用特定于環境的值替換以下預留位置：
- `$MySubscriptionId`- 包含要限制許可權的 Azure Cosmos 帳戶的訂閱 ID。 例如：`e5c8766a-eeb0-40e8-af56-0eb142ebf78e`。
- `$MyResourceGroupName`- 包含 Azure Cosmos 帳戶的資源組。 例如：`myresourcegroup`。
- `$MyAzureCosmosDBAccountName`- Azure 宇宙帳戶的名稱。 例如：`mycosmosdbsaccount`。
- `$MyUserName`- 要限制username@domain存取權限的使用者的登錄名 （ ） 例如：`cosmosdbuser@contoso.com`。

## <a name="select-your-azure-subscription"></a>選取您的 Azure 訂用帳戶

Azure PowerShell 命令要求您登錄並選擇訂閱以執行命令：

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>創建自訂 Azure 活動目錄角色

以下腳本為 Azure Cosmos 帳戶創建具有"僅鍵"存取權限的 Azure 活動目錄角色指派。 該角色基於[Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)和[Azure Cosmos DB 的細微性操作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)。 這些角色和操作是 Azure`Microsoft.DocumentDB`活動目錄命名空間的一部分。

1. 首先，創建一個 JSON`AzureCosmosKeyOnlyAccess.json`文檔，其中指定包含以下內容：

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. 運行以下命令以創建角色指派並將其分配給使用者：

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>禁止執行非資料操作

以下命令刪除了使用鍵以：
- 創建、修改或刪除資源
- 更新容器設置（包括索引策略、輸送量等）。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Cosmos DB 基於角色的存取控制](role-based-access-control.md)
- 獲取[對 Cosmos DB 中資料的安全訪問](secure-access-to-data.md)概述
