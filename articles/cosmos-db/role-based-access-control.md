---
title: Azure Cosmos DB 中的角色型存取控制
description: 瞭解 Azure Cosmos DB 如何透過 Active directory 整合（RBAC）提供資料庫保護。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 858e185a0e4fa406fb4645475673acc13a0d37f3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87086668"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的角色型存取控制

Azure Cosmos DB 提供內建的角色型存取控制（RBAC），適用于 Azure Cosmos DB 中的常見管理案例。 在 Azure Active Directory 中具有設定檔的個人可以將這些 RBAC 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕對 Azure Cosmos DB 資源的資源和作業的存取權。 角色指派的範圍僅限於控制平面存取，其中包括 Azure Cosmos 帳戶、資料庫、容器和供應專案（輸送量）的存取權。

## <a name="built-in-roles"></a>內建角色

以下是 Azure Cosmos DB 支援的內建角色：

|**內建角色**  |**描述**  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。|
|[Cosmos DB 帳戶讀者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。|
|[Cosmos 備份操作員](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交 Azure Cosmos 資料庫或容器的還原要求。|
|[Cosmos DB 操作員](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以布建 Azure Cosmos 帳戶、資料庫和容器，但無法存取存取資料所需的金鑰。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援僅適用于控制平面作業。 使用主要金鑰或資源權杖來保護資料平面作業。 若要深入瞭解，請參閱[安全存取 Azure Cosmos DB 中的資料](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>身分識別與存取管理（IAM）

Azure 入口網站中的 [**存取控制（IAM）** ] 窗格用來設定 Azure Cosmos 資源上的角色型存取控制。 這些角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建角色或自訂角色。 下列螢幕擷取畫面顯示使用 Azure 入口網站中的存取控制（IAM） Active Directory 整合（RBAC）：

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure 入口網站中的存取控制 (IAM) - 示範資料庫安全性":::

## <a name="custom-roles"></a>自訂角色

除了內建角色以外，使用者也可以在 Azure 中建立[自訂角色](../role-based-access-control/custom-roles.md)，並將這些角色套用至其 Active Directory 租使用者中所有訂用帳戶的服務主體。 自訂角色可讓使用者以一組自訂的資源提供者作業來建立 RBAC 角色定義。 若要瞭解哪些作業可用來建立的自訂角色 Azure Cosmos DB 參閱[Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>防止 Azure Cosmos DB Sdk 的變更

Azure Cosmos DB 資源提供者可以鎖定，以防止用戶端使用帳戶金鑰（也就是透過 Azure Cosmos SDK 連線的應用程式）所進行的資源變更。 這也包括從 Azure 入口網站進行的變更。 對於需要更高程度控制和管理生產環境的使用者而言，這項功能可能是理想的做法。 防止 SDK 的變更也會啟用控制平面作業的資源鎖定和診斷記錄等功能。 從 Azure Cosmos DB SDK 連接的用戶端將無法變更 Azure Cosmos 帳戶、資料庫、容器和輸送量的任何屬性。 涉及讀取和寫入資料至 Cosmos 容器本身的作業不會受到影響。

啟用這項功能時，任何資源的變更都只能從具有正確 RBAC 角色的使用者進行，並 Azure Active Directory 認證，包括受控服務識別。

> [!WARNING]
> 啟用此功能可能會對您的應用程式造成影響。 啟用之前，請確定您瞭解其影響。

### <a name="check-list-before-enabling"></a>啟用前檢查清單

此設定可防止任何使用帳戶金鑰進行連線的用戶端進行任何 Cosmos 資源的變更，包括任何 Cosmos DB SDK、透過帳戶金鑰連接的任何工具，或從 Azure 入口網站。 若要在啟用這項功能之後避免應用程式發生問題，請檢查應用程式或 Azure 入口網站使用者是否執行下列任何動作，再啟用此功能，包括：

- Cosmos 帳戶的任何變更，包括任何屬性或新增或移除區域。

- 建立、刪除子資源（例如資料庫和容器）。 這包括適用于其他 Api 的資源，例如 Cassandra、MongoDB、Gremlin 和資料表資源。

- 正在更新資料庫或容器層級資源的輸送量。

- 修改容器屬性，包括索引原則、TTL 和唯一金鑰。

- 修改預存程式、觸發程式或使用者定義函數。

如果您的應用程式（或透過 Azure 入口網站的使用者）執行上述任何動作，則必須將其遷移至透過[ARM 範本](manage-sql-with-resource-manager.md)、 [PowerShell](manage-with-powershell.md)、 [Azure CLI](manage-with-cli.md)、REST 或[Azure 管理程式庫](https://github.com/Azure-Samples/cosmos-management-net)執行。 請注意，Azure 管理有[多種語言](https://docs.microsoft.com/azure/?product=featured#languages-and-tools)版本。

### <a name="set-via-arm-template"></a>透過 ARM 範本設定

若要使用 ARM 範本設定此屬性，請更新現有的範本，或為您目前的部署匯出新的範本，然後將加入 `"disableKeyBasedMetadataWriteAccess": true` 至資源的屬性 `databaseAccounts` 。 以下是使用此屬性設定之 Azure Resource Manager 範本的基本範例。

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-04-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

> [!IMPORTANT]
> 使用此屬性 redploying 時，請確定您包含帳戶和子資源的其他屬性。 請勿將此範本部署為 [否]，否則將會重設所有的帳戶屬性。

### <a name="set-via-azure-cli"></a>設定 via Azure CLI

若要使用 Azure CLI 來啟用，請使用下列命令：

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>透過 PowerShell 設定

若要使用 Azure PowerShell 來啟用，請使用下列命令：

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 角色型存取控制（Azure RBAC）](../role-based-access-control/overview.md)
- [Azure 自訂角色](../role-based-access-control/custom-roles.md) (機器翻譯)
- [Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
