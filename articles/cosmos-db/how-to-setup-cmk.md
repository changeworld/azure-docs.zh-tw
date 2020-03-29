---
title: 為 Azure Cosmos 資料庫帳戶配置客戶管理的金鑰
description: 瞭解如何使用 Azure 金鑰保存庫為 Azure Cosmos DB 帳戶配置客戶管理金鑰
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 6e2a90b8f81b9b945905ee98beb1686c54a62e8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063772"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure 金鑰保存庫為 Azure Cosmos 帳戶配置客戶管理金鑰

> [!NOTE]
> 此時，您必須請求訪問才能使用此功能。 為此，請聯繫[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)。

存儲在 Azure Cosmos 帳戶中的資料會自動無縫加密，使用 Microsoft 管理的**金鑰（服務管理金鑰**）。 或者，您可以選擇使用您管理的金鑰（**客戶管理的金鑰**）添加第二層加密。

![圍繞客戶資料的加密層](./media/how-to-setup-cmk/cmk-intro.png)

必須在[Azure 金鑰保存庫中](../key-vault/key-vault-overview.md)存儲客戶管理的金鑰，並為使用客戶管理的金鑰啟用的每個 Azure Cosmos 帳戶提供金鑰。 此金鑰用於加密該帳戶中存儲的所有資料。

> [!NOTE]
> 目前，客戶管理的金鑰僅適用于新的 Azure Cosmos 帳戶。 應在帳戶創建期間配置它們。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>註冊 Azure 訂閱的 Azure 宇宙資料庫資來源提供者

1. 登錄到 Azure[門戶](https://portal.azure.com/)，轉到 Azure 訂閱，並在 **"設置"** 選項卡下選擇**資來源提供者**：

   ![左側功能表中的"資來源提供者"條目](./media/how-to-setup-cmk/portal-rp.png)

1. 搜索**Microsoft.DocumentDB**資來源提供者。 驗證資來源提供者是否已標記為已註冊。 如果沒有，請選擇資來源提供者並選擇 **"註冊**：

   ![註冊 Microsoft.DocumentDB 資來源提供者](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>配置 Azure 金鑰保存庫實例

使用 Azure Cosmos DB 的客戶管理金鑰需要您在 Azure 金鑰保存庫實例上設置兩個屬性，並計畫用於託管加密金鑰。 這些屬性包括 **"虛刪除****"和"不清除**"。 預設情況下，這些屬性未啟用。 可以使用 PowerShell 或 Azure CLI 啟用它們。

要瞭解如何在現有 Azure 金鑰保存庫實例上啟用這些屬性，請參閱以下文章中的"啟用虛刪除"和"啟用清除保護"部分：

- [如何使用電源Shell虛刪除](../key-vault/key-vault-soft-delete-powershell.md)
- [如何使用 Azure CLI 虛刪除](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>向 Azure 金鑰保存庫實例添加訪問策略

1. 從 Azure 門戶轉到計畫用於託管加密金鑰的 Azure 金鑰保存庫實例。 從左側功能表中選擇**訪問策略**：

   ![左側功能表中的"訪問策略"](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 選擇 **= 添加訪問策略**。

1. 在 **"鍵許可權**下拉式功能表"下，**選擇"獲取**"、"**取消包裝金鑰**"和 **"包裝金鑰**"許可權：

   ![選擇正確的許可權](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在 **"選擇主體**"下，選擇 **"無選擇**"。 然後，搜索**Azure Cosmos DB**主體並選擇它（為了更容易查找，還可以按主體 ID 搜索：`a232010e-820c-4083-83bb-3ace5fc29d0b`除主 ID 所在的`57506a73-e302-42a9-b869-6f12d9ec29e9`Azure 政府區域外的任何 Azure 區域。 最後，**選擇底部的"選擇**"。 如果**Azure Cosmos DB**主體不在清單中，則可能需要重新註冊**Microsoft.DocumentDB**資來源提供者，如[註冊本文的資來源提供者](#register-resource-provider)部分所述。

   ![選擇 Azure 宇宙資料庫主體](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 選擇 **"添加**"以添加新的訪問策略。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure 金鑰保存庫中生成金鑰

1. 從 Azure 門戶轉到計畫用於託管加密金鑰的 Azure 金鑰保存庫實例。 然後，從左側功能表中選擇 **"鍵**"：

   ![左側功能表中的"鍵"條目](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 選擇 **"生成/導入**"，為新金鑰提供名稱，然後選擇 RSA 金鑰大小。 建議至少 3072 提供最佳安全性。 然後選擇 **"創建**"

   ![建立新的金鑰](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 創建金鑰後，選擇新創建的金鑰，然後選擇其當前版本。

1. 複製金鑰**的金鑰識別碼**，最後一個前斜杠後部件除外：

   ![複製金鑰的金鑰識別碼](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>創建新的 Azure 宇宙帳戶

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

從 Azure 門戶創建新的 Azure Cosmos DB 帳戶時，在**加密**步驟中選擇**客戶管理的金鑰**。 在 **"金鑰 URI"** 欄位中，粘貼從上一步驟複製的 Azure 金鑰保存庫金鑰的 URI/鍵識別碼：

![在 Azure 門戶中設置 CMK 參數](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用 PowerShell 創建新的 Azure Cosmos DB 帳戶時：

- 傳遞之前在**屬性物件**中的**keyVaultKeyUri**屬性下複製的 Azure 金鑰保存庫金鑰的 URI。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須顯式設置`Location`參數，才能使用客戶管理的金鑰成功創建帳戶。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

通過 Azure 資源管理器範本創建新的 Azure Cosmos 帳戶時：

- 傳遞前面在**屬性**物件中的**keyVaultKeyUri**屬性下複製的 Azure 金鑰庫金鑰的 URI。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須顯式設置`Location`參數，才能使用客戶管理的金鑰成功創建帳戶。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

使用以下 PowerShell 腳本部署範本：

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a>使用 Azure CLI

通過 Azure CLI 創建新的 Azure Cosmos 帳戶時，請傳遞之前在 **--key-uri**參數下複製的 Azure 金鑰保存庫金鑰的 URI。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>使用客戶管理的金鑰是否收取額外費用？

是。 為了考慮使用客戶管理的金鑰管理資料加密和解密所需的額外計算負載，針對 Azure Cosmos 帳戶執行的所有操作都使用[請求單元](./request-units.md)增加 25%。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些資料會使用客戶管理的金鑰進行加密？

存儲在 Azure Cosmos 帳戶中的所有資料都使用客戶管理的金鑰進行加密，但以下中繼資料除外：

- Azure Cosmos DB[帳戶、資料庫和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名稱

- [預存程序](./stored-procedures-triggers-udfs.md)的名稱

- [索引策略](./index-policy.md)中聲明的屬性路徑

- 容器[分區鍵](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>現有 Azure Cosmos 帳戶支援客戶管理的金鑰嗎？

此功能當前僅適用于新帳戶。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有計劃支援比帳戶級金鑰更精細的細微性？

當前未，但正在考慮容器級金鑰。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客戶管理的金鑰如何影響備份？

Azure Cosmos DB 會[定期和自動備份](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)存儲在帳戶中的資料。 此操作將備份加密的資料。 要使用還原的備份，需要在備份時使用的加密金鑰。 這意味著未進行吊銷，並且仍將啟用備份時使用的金鑰版本。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何撤銷加密金鑰？

通過禁用最新版本的金鑰來完成金鑰吊銷：

![禁用金鑰版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，要撤銷 Azure 金鑰保存庫實例中的所有金鑰，可以刪除授予 Azure Cosmos DB 主體的訪問策略：

![刪除 Azure Cosmos DB 主體的訪問策略](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤銷客戶管理的金鑰後，哪些操作可用？

吊銷加密金鑰時可能的唯一操作是刪除帳戶。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Azure 宇宙 DB 中資料加密](./database-encryption-at-rest.md)的更多詳細資訊。
- 獲取[對 Cosmos DB 中資料的安全訪問](secure-access-to-data.md)概述。
