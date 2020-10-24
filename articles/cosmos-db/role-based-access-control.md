---
title: Azure Cosmos DB 中的角色型存取控制
description: 瞭解 Azure Cosmos DB 如何使用 (RBAC) 的 Active directory 整合來提供資料庫保護。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: mjbrown
ms.openlocfilehash: c9a29a34b25b6834c2c343bd568f3dd3548ceff3
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92474932"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Azure Cosmos DB 中的角色型存取控制

Azure Cosmos DB 針對 Azure Cosmos DB 中常見的管理案例，提供內建的角色型存取控制 (RBAC) 。 在 Azure Active Directory 中具有設定檔的個人可以將這些 Azure 角色指派給使用者、群組、服務主體或受控識別，以授與或拒絕對 Azure Cosmos DB 資源的資源和作業的存取權。 角色指派的範圍僅限於控制平面存取，其中包括存取 Azure Cosmos 帳戶、資料庫、容器，以及 (輸送量) 的優惠。

## <a name="built-in-roles"></a>內建角色

以下是 Azure Cosmos DB 支援的內建角色：

|**內建角色**  |**描述**  |
|---------|---------|
|[DocumentDB 帳戶參與者](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|可以管理 Azure Cosmos DB 帳戶。|
|[Cosmos DB 帳戶讀取者](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|可以讀取 Azure Cosmos DB 帳戶資料。|
|[Cosmos Backup 運算子](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|可以提交 Azure Cosmos 資料庫或容器的還原要求。 無法存取任何資料或使用資料總管。|
|[Cosmos DB 操作員](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|可以布建 Azure Cosmos 帳戶、資料庫和容器。 無法存取任何資料或使用資料總管。|

> [!IMPORTANT]
> Azure Cosmos DB 中的 RBAC 支援僅適用于控制平面作業。 使用主要金鑰或資源權杖來保護資料平面作業。 若要深入瞭解，請參閱 [安全存取 Azure Cosmos DB 中的資料](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>身分識別和存取管理 (IAM) 

Azure 入口網站中的 **存取控制 (IAM) ** 窗格可用來設定 Azure Cosmos 資源上的角色型存取控制。 角色會套用至 Active Directory 中的使用者、群組、服務主體和受控識別。 您可以針對個人和群組使用內建角色或自訂角色。 下列螢幕擷取畫面顯示在) 中使用存取控制 (IAM Azure 入口網站 Active Directory 整合 (RBAC) ：

:::image type="content" source="./media/role-based-access-control/database-security-identity-access-management-rbac.png" alt-text="Azure 入口網站中的存取控制 (IAM) - 示範資料庫安全性":::

## <a name="custom-roles"></a>自訂角色

除了內建角色之外，使用者也可以在 Azure 中建立 [自訂角色](../role-based-access-control/custom-roles.md) ，並將這些角色套用至其 Active Directory 租使用者中所有訂用帳戶的服務主體。 自訂角色可讓使用者使用一組自訂的資源提供者作業來建立 Azure 角色定義。 若要瞭解哪些作業可用於建立 Azure Cosmos DB 的自訂角色，請參閱 [Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

> [!TIP]
> 需要存取儲存在 Cosmos DB 中的資料或在 Azure 入口網站中使用資料總管的自訂角色必須具有 `Microsoft.DocumentDB/databaseAccounts/listKeys/*` 動作。

## <a name="preventing-changes-from-the-azure-cosmos-db-sdks"></a><a id="prevent-sdk-changes"></a>防止 Azure Cosmos DB Sdk 的變更

您可以鎖定 Azure Cosmos DB 資源提供者，以防止用戶端使用帳戶金鑰進行連線的任何資源變更， (是透過 Azure Cosmos SDK) 連線的應用程式。 這也包含 Azure 入口網站所做的變更。 對於希望生產環境具有更高程度的控制和治理能力的使用者而言，這項功能可能是理想的選擇。 防止 SDK 的變更也可啟用資源鎖定和診斷記錄等功能，以進行控制平面作業。 從 Azure Cosmos DB SDK 連線的用戶端將無法變更 Azure Cosmos 帳戶、資料庫、容器和輸送量的任何屬性。 涉及讀取和寫入資料至 Cosmos 容器本身的作業不會受到影響。

啟用這項功能時，您只能從具有適當 Azure 角色的使用者進行變更，並 Azure Active Directory 包含受控服務身分識別的認證。

> [!WARNING]
> 啟用此功能可能會對您的應用程式造成影響。 在啟用之前，請確定您已瞭解其影響。

### <a name="check-list-before-enabling"></a>啟用前檢查清單

這項設定可防止任何用戶端使用帳戶金鑰（包括任何 Cosmos DB SDK、透過帳戶金鑰連接的工具，或從 Azure 入口網站連接的任何 Cosmos 資源）進行任何變更。 若要在啟用這項功能之後防止應用程式發生問題，請檢查應用程式或 Azure 入口網站使用者是否在啟用這項功能之前執行下列任何動作，包括：

- Cosmos 帳戶的任何變更，包括任何屬性，或新增或移除區域。

- 建立、刪除子資源，例如資料庫和容器。 這包括其他 Api 的資源，例如 Cassandra、MongoDB、Gremlin 和資料表資源。

- 正在更新資料庫或容器層級資源的輸送量。

- 修改容器屬性，包括索引原則、TTL 和唯一索引鍵。

- 修改預存程式、觸發程式或使用者定義函數。

如果您的應用程式 (或使用者透過 Azure 入口網站) 執行上述任何一項動作，就必須將它們遷移至透過 [ARM 範本](./manage-with-templates.md)、 [PowerShell](manage-with-powershell.md)、 [Azure CLI](manage-with-cli.md)、REST 或 [Azure 管理程式庫](https://github.com/Azure-Samples/cosmos-management-net)來執行。 請注意，Azure 管理提供 [多種語言](/azure/?product=featured#languages-and-tools)。

### <a name="set-via-arm-template"></a>透過 ARM 範本設定

若要使用 ARM 範本來設定此屬性，請更新現有的範本，或為目前的部署匯出新範本，然後將加入 `"disableKeyBasedMetadataWriteAccess": true` 至資源的屬性 `databaseAccounts` 。 以下是使用此屬性設定 Azure Resource Manager 範本的基本範例。

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
> Redploying 此屬性時，請確定您包含帳戶和子資源的其他屬性。 請勿依原樣部署此範本，否則會重設所有的帳戶屬性。

### <a name="set-via-azure-cli"></a>透過 Azure CLI 設定

若要使用 Azure CLI 啟用，請使用下列命令：

```azurecli-interactive
az cosmosdb update  --name [CosmosDBAccountName] --resource-group [ResourceGroupName]  --disable-key-based-metadata-write-access true

```

### <a name="set-via-powershell"></a>透過 PowerShell 設定

若要使用 Azure PowerShell 啟用，請使用下列命令：

```azurepowershell-interactive
Update-AzCosmosDBAccount -ResourceGroupName [ResourceGroupName] -Name [CosmosDBAccountName] -DisableKeyBasedMetadataWriteAccess true
```

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 角色型存取控制 (Azure RBAC) ](../role-based-access-control/overview.md)
- [Azure 自訂角色](../role-based-access-control/custom-roles.md) (機器翻譯)
- [Azure Cosmos DB 資源提供者作業](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)