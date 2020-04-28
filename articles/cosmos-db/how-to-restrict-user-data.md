---
title: 限制使用者只能使用 Azure Cosmos DB 存取資料作業
description: 瞭解如何使用 Azure Cosmos DB 來限制資料作業的存取
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980370"
---
# <a name="restrict-user-access-to-data-operations-only"></a>限制使用者只能存取資料作業

在 Azure Cosmos DB 中，有兩種方式可驗證與資料庫服務的互動：
- 與 Azure 入口網站互動時，使用您的 Azure Active Directory 身分識別，
- 在發出來自 Api 和 Sdk 的呼叫時，使用 Azure Cosmos DB[金鑰](secure-access-to-data.md#master-keys)或[資源權杖](secure-access-to-data.md#resource-tokens)。

每個驗證方法都會提供不同作業集的存取權，但有![一些重迭：每個驗證類型的作業分割](./media/how-to-restrict-user-data/operations.png)

在某些情況下，您可能會想要限制組織中的某些使用者只能執行資料作業（也就是 CRUD 要求和查詢）。 這種情況通常適用于不需要建立或刪除資源的開發人員，或變更其所處理之容器的布建輸送量。

您可以套用下列步驟來限制存取：
1. 為您想要限制存取權的使用者建立自訂 Azure Active Directory 角色。 自訂 Active Directory 角色應使用 Azure Cosmos DB 的[細微動作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)，針對作業提供更細緻的存取層級。
1. 不允許使用索引鍵執行非資料作業。 若要達到此目的，您可以將這些作業限制為僅 Azure Resource Manager 呼叫。

本文的下一節會示範如何執行這些步驟。

> [!NOTE]
> 若要在接下來的幾節中執行命令，您必須安裝 Azure PowerShell 模組3.0.0 或更新版本，以及您嘗試修改之訂用帳戶上的[Azure 擁有者角色](../role-based-access-control/built-in-roles.md#owner)。

在下一節中的 PowerShell 腳本中，以您環境特定的值取代下列預留位置：
- `$MySubscriptionId`-訂用帳戶識別碼，其中包含您想要限制許可權的 Azure Cosmos 帳戶。 例如： `e5c8766a-eeb0-40e8-af56-0eb142ebf78e` 。
- `$MyResourceGroupName`-包含 Azure Cosmos 帳戶的資源群組。 例如： `myresourcegroup` 。
- `$MyAzureCosmosDBAccountName`-您的 Azure Cosmos 帳戶名稱。 例如： `mycosmosdbsaccount` 。
- `$MyUserName`-您想要username@domain限制存取權之使用者的登入（）。 例如： `cosmosdbuser@contoso.com` 。

## <a name="select-your-azure-subscription"></a>選取您的 Azure 訂用帳戶

Azure PowerShell 命令會要求您登入並選取要執行命令的訂用帳戶：

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>建立自訂 Azure Active Directory 角色

下列腳本會建立具有 Azure Cosmos 帳戶「僅金鑰」存取權的 Azure Active Directory 角色指派。 角色是[以適用于 Azure 資源的自訂角色](../role-based-access-control/custom-roles.md)和[Azure Cosmos DB 的細微動作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)為基礎。 這些角色和動作是`Microsoft.DocumentDB` Azure Active Directory 命名空間的一部分。

1. 首先，使用下列內容建立名`AzureCosmosKeyOnlyAccess.json`為的 JSON 檔：

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

1. 執行下列命令來建立角色指派，並將其指派給使用者：

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>不允許執行非資料作業

下列命令會移除使用金鑰的功能：
- 建立、修改或刪除資源
- 更新容器設定（包括索引編制原則、輸送量等等）。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Cosmos DB 的角色型存取控制](role-based-access-control.md)
- 深入瞭解[Cosmos DB 中資料的安全存取](secure-access-to-data.md)
