---
title: 防止刪除或變更 Azure Cosmos DB 資源
description: 使用 Azure 資源鎖定來防止 Azure Cosmos DB 資源遭到刪除或變更。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: 6db7bbf03b023a80032a4ed15f2f4f82dd2b0b98
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088364"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>防止刪除或變更 Azure Cosmos DB 資源
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

如果您是系統管理員，您可能需要鎖定 Azure Cosmos 帳戶、資料庫或容器，以防止組織中的其他使用者不小心刪除或修改重要資源。 您可以將鎖定層級設定為 CanNotDelete 或 ReadOnly。

- **CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但無法刪除資源。
- **ReadOnly** 表示經過授權的使用者可以讀取資源，但無法刪除或更新資源。 套用這個鎖定類似於限制所有經過授權使用者的權限是由「讀取者」角色所授與。

## <a name="how-locks-are-applied"></a>如何套用鎖定

當您在父範圍套用鎖定時，該範圍內的所有資源都會都繼承相同的鎖定。 甚至您稍後新增的資源都會繼承父項的鎖定。 繼承中限制最嚴格的鎖定優先順序最高。

不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制。 若要瞭解 Azure Cosmos DB 的 RBAC，請參閱 [Azure Cosmos DB 中以角色為基礎的存取控制](role-based-access-control.md)。

Resource Manager 鎖定只會套用於管理平面發生的作業，亦即要傳送至 https://management.azure.com 的作業。 鎖定並不會限制資源執行自己功能的方式。 已限制資源變更，但未限制資源作業。 例如，Azure Cosmos 容器上的 ReadOnly 鎖定會防止您刪除或修改容器。 它不會讓您無法建立、更新或刪除容器中的資料。 允許資料交易，因為那些作業並不會傳送到 https://management.azure.com。

## <a name="manage-locks"></a>管理鎖定

> [!WARNING]
> 除非先藉由啟用 disableKeyBasedMetadataWriteAccess 屬性來鎖定 Azure Cosmos 帳戶，否則使用帳戶金鑰存取 Azure Cosmos DB 的使用者所做的變更無法使用資源鎖定。 啟用此屬性之前請務必小心，以確保它不會中斷現有的應用程式，這些應用程式會使用任何 SDK、Azure 入口網站或協力廠商工具透過帳戶金鑰進行連線，以及修改資源（例如變更輸送量、更新索引原則等等）來變更資源。若要深入瞭解並進行檢查清單，以確保您的應用程式可繼續運作，請 [防止 Azure Cosmos DB sdk 的變更](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Azure CLI

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>範本

將鎖定套用至 Azure Cosmos DB 資源時，請使用下列格式：

- name - `{resourceName}/Microsoft.Authorization/{lockName}`
- type - `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> 在修改現有的 Azure Cosmos 帳戶時，請務必在使用此屬性 redploying 時，包含帳戶和子資源的其他屬性。 請勿依原樣部署此範本，否則會重設所有的帳戶屬性。

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>下一步

- [Azure Resource Manager 鎖定的總覽](../azure-resource-manager/management/lock-resources.md)
