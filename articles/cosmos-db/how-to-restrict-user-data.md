---
title: 限制使用者只能使用 Azure Cosmos DB 存取資料作業
description: 瞭解如何只使用 Azure Cosmos DB 來限制對資料作業的存取
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 44a62643c459fb61e7a2a95c2a9dd55ea4f19111
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570653"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>限制使用者存取 Azure Cosmos DB 中的資料作業

在 Azure Cosmos DB 中，有兩種方式可以驗證與資料庫服務的互動：

- 與 Azure 入口網站互動時，使用您的 Azure Active Directory 身分識別，
- 從 Api 和 Sdk 發出呼叫時，使用 Azure Cosmos DB [金鑰](secure-access-to-data.md#primary-keys) 或 [資源權杖](secure-access-to-data.md#resource-tokens) 。

每個驗證方法都可讓您存取不同的作業集，但有一些重迭：

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="每一驗證類型的作業分割" border="false":::

在某些情況下，您可能會想要限制組織的某些使用者只執行 (的 CRUD 要求和查詢) 的資料作業。 這種情況通常適用于不需要建立或刪除資源的開發人員，或變更其正在處理之容器的布建輸送量。

您可以套用下列步驟來限制存取：
1. 為您想要限制存取權的使用者建立自訂 Azure Active Directory 角色。 自訂 Active Directory 角色應使用 Azure Cosmos DB 的 [細微動作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)，對作業進行更細緻的存取層級。
1. 不允許使用索引鍵執行非資料作業。 您可以藉由將這些作業限制為僅 Azure Resource Manager 呼叫來達到此目的。

本文的後續各節會示範如何執行這些步驟。

> [!NOTE]
> 若要在接下來的幾節中執行命令，您必須安裝 Azure PowerShell Module 3.0.0 或更新版本，以及您嘗試修改的訂用帳戶上的 [Azure 擁有者角色](../role-based-access-control/built-in-roles.md#owner) 。

在下一節的 PowerShell 腳本中，以您的環境特有的值取代下列預留位置：
- `$MySubscriptionId` -包含您想要限制許可權之 Azure Cosmos 帳戶的訂用帳戶識別碼。 例如： `e5c8766a-eeb0-40e8-af56-0eb142ebf78e` 。
- `$MyResourceGroupName` -包含 Azure Cosmos 帳戶的資源群組。 例如： `myresourcegroup` 。
- `$MyAzureCosmosDBAccountName` -Azure Cosmos 帳戶的名稱。 例如： `mycosmosdbsaccount` 。
- `$MyUserName` -登入 (username@domain 您要限制其存取權的使用者) 。 例如： `cosmosdbuser@contoso.com` 。

## <a name="select-your-azure-subscription"></a>選取您的 Azure 訂用帳戶

Azure PowerShell 命令會要求您登入，並選取要執行命令的訂用帳戶：

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>建立自訂 Azure Active Directory 角色

下列腳本會使用 Azure Cosmos 帳戶的「僅限金鑰」存取來建立 Azure Active Directory 角色指派。 角色是根據 [Azure 自訂角色](../role-based-access-control/custom-roles.md) 以及 [Azure Cosmos DB 的細微動作](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)。 這些角色和動作是 `Microsoft.DocumentDB` Azure Active Directory 命名空間的一部分。

1. 首先，使用下列內容建立名為的 JSON 檔 `AzureCosmosKeyOnlyAccess.json` ：

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

下列命令會移除使用金鑰的能力：
- 建立、修改或刪除資源
- 更新容器設定 (包括編制索引原則、輸送量等 ) 。

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Cosmos DB 的角色型存取控制](role-based-access-control.md)
- 取得[Cosmos DB 中資料的安全存取](secure-access-to-data.md)總覽
