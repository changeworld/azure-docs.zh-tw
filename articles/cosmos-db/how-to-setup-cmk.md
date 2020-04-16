---
title: 為 Azure Cosmos 資料庫帳戶設定客戶管理的金鑰
description: 瞭解如何使用 Azure 金鑰保管庫為 Azure Cosmos DB 帳戶設定客戶管理金鑰
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 733a85e492185e7e83922a3cc91d53c848b939a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411154"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure 金鑰保存庫為 Azure Cosmos 帳戶設定客戶管理金鑰

> [!NOTE]
> 此時,您必須請求訪問才能使用此功能。 此,請聯繫[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)。

存儲在 Azure Cosmos 帳戶中的數據會自動無縫加密,使用 Microsoft 管理的**密鑰(服務管理金鑰**)。 或者,您可以選擇使用您管理的密鑰(**客戶管理的密鑰**)添加第二層加密。

![以客戶資料的加密層](./media/how-to-setup-cmk/cmk-intro.png)

必須在[Azure 金鑰保存式庫中](../key-vault/key-vault-overview.md)儲存客戶管理的金鑰,並為使用客戶管理的金鑰啟用的每個 Azure Cosmos 帳戶提供金鑰。 此密鑰用於加密該帳戶中存儲的所有數據。

> [!NOTE]
> 目前,客戶管理的金鑰僅適用於新的 Azure Cosmos 帳戶。 應在帳戶創建期間配置它們。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>註冊 Azure 訂閱的 Azure 宇宙資料庫資源提供者

1. 登入 Azure[門戶](https://portal.azure.com/),轉到 Azure 訂閱,並在 **「設定」** 選項卡下選擇**資源提供者 :**

   ![左方選單中的「資源提供者」 項目](./media/how-to-setup-cmk/portal-rp.png)

1. 搜索**Microsoft.DocumentDB**資源提供程式。 驗證資源提供程式是否已標記為已註冊。 如果沒有,請選擇資源提供者並選擇 **「註冊**:

   ![註冊 Microsoft.DocumentDB 資源提供者](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>設定 Azure 金鑰保存庫實例

使用 Azure Cosmos DB 的客戶管理金鑰需要您在 Azure 密鑰保管庫實例上設置兩個屬性,並計劃用於託管加密密鑰。 這些屬性包括 **「軟刪除****」和「不清除**」。 默認情況下,這些屬性未啟用。 可以使用 PowerShell 或 Azure CLI 啟用它們。

要瞭解如何在現有 Azure 金鑰保管庫實例上啟用這些屬性,請參閱以下文章中的「啟用軟刪除」和「啟用清除保護」部分:

- [如何使用電源Shell軟移除](../key-vault/key-vault-soft-delete-powershell.md)
- [如何使用 Azure CLI 軟刪除](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>加入 Azure 金鑰保存庫實例

1. 從 Azure 門戶轉到計劃用於託管加密密鑰的 Azure 密鑰保管庫實例。 從左方選單中選擇**存取政策**:

   ![左側功能表中的"訪問策略"](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 選擇 **= 新增存取原則**。

1. 在 **「鍵權限**下拉選單」**選擇「 獲取 」、「****取消包裝金鑰**」**─「包裝金鑰**」 許可權:

   ![選擇正確的權限](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在 **「選擇主體**」下,選擇 **「無選擇**」。 然後,搜索**Azure Cosmos DB**主體並選擇它(為了更容易查找,還可以按`a232010e-820c-4083-83bb-3ace5fc29d0b`主體 ID`57506a73-e302-42a9-b869-6f12d9ec29e9`搜索: 除主 ID 所在的 Azure 政府區域外的任何 Azure 區域。 最後,**選擇底部的"** 如果**Azure Cosmos DB**主體不在清單中,則可能需要重新註冊**Microsoft.DocumentDB**資源提供程式,如[註冊本文的資源提供程式](#register-resource-provider)部分所述。

   ![選擇 Azure 宇宙資料庫主體](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 選擇 **「新增**」以添加新的存取策略。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure 金鑰保存庫中產生金鑰

1. 從 Azure 門戶轉到計劃用於託管加密密鑰的 Azure 密鑰保管庫實例。 然後,從左側選單中選擇 **「鍵**」:

   ![左方選單中的「鍵」項目](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 選擇 **"生成/匯入**",為新密鑰提供名稱,然後選擇 RSA 金鑰大小。 建議至少 3072 提供最佳安全性。 然後選擇 **"建立**"

   ![建立新的金鑰](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 建立金鑰後,選擇新創建的密鑰,然後選擇其當前版本。

1. 複製金鑰**的金鑰識別碼**,最後一個前斜杠後元件除外:

   ![複製金鑰的金鑰識別碼](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>建立新的 Azure 宇宙帳戶

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

從 Azure 門戶建立新的 Azure Cosmos DB 帳號時,在**加密**步驟中選擇**客戶管理的金鑰**。 在 **"金鑰 URI"** 欄位中,貼上一步驟複製的 Azure 金鑰保管庫金鑰的 URI/鍵識別碼:

![在 Azure 門戶中設定 CMK 參數](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用 PowerShell 建立新的 Azure Cosmos DB 帳號時:

- 傳遞之前在**屬性物件**中的**keyVaultKeyUri**屬性下複製的 Azure 密鑰保管庫密鑰的 URI。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須顯式設定`Location`參數,才能使用客戶管理的密鑰成功創建帳戶。

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

透過 Azure 資源管理員樣本建立新的 Azure Cosmos 帳號時:

- 傳遞前面在**屬性**物件中的**keyVaultKeyUri**屬性下複製的 Azure 密鑰庫金鑰的 URI。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須顯式設定`Location`參數,才能使用客戶管理的密鑰成功創建帳戶。

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

使用以下 PowerShell 文稿部署樣本:

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

通過 Azure CLI 建立新的 Azure Cosmos 帳戶時,請傳遞之前在 **--key-uri**參數下複製的 Azure 金鑰保管庫金鑰的 URI。

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

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>使用客戶管理的金鑰是否收取額外費用?

是。 為了考慮使用客戶管理的密鑰管理數據加密和解密所需的額外計算負載,針對 Azure Cosmos 帳戶執行的所有操作都使用[請求單元](./request-units.md)增加 25%。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些數據會使用客戶管理的密鑰進行加密?

儲存在 Azure Cosmos 帳號中的所有資料都使用客戶管理的金鑰進行加密,但以下中繼資料除外:

- Azure Cosmos DB[帳號、資料庫和容器](./account-overview.md#elements-in-an-azure-cosmos-account)名稱

- [儲存過程](./stored-procedures-triggers-udfs.md)的名稱

- [索引原則](./index-policy.md)中聲明的屬性路徑

- 容器[分割區鍵](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>現有 Azure Cosmos 帳戶支援客戶管理的密鑰嗎?

此功能當前僅適用於新帳戶。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有計劃支援比帳戶級密鑰更精細的粒度?

當前未,但正在考慮容器級密鑰。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客戶管理的密鑰如何影響備份?

Azure Cosmos DB 會[定期和自動備份](./online-backup-and-restore.md)存儲在帳戶中的數據。 此操作將備份加密的數據。 要使用還原的備份,需要在備份時使用的加密密鑰。 這意味著未進行吊銷,並且仍將啟用備份時使用的密鑰版本。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何撤銷加密金鑰?

透過關閉最新版本的金鑰來完成金鑰吊銷:

![關閉金鑰版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者,要撤銷 Azure 金鑰保管庫實例中的所有密鑰,可以刪除授予 Azure Cosmos DB 主體的存取策略:

![移除 Azure Cosmos DB 的存取原則](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤銷客戶管理的金鑰後,哪些操作可用?

吊銷加密金鑰時可能的唯一操作是刪除帳戶。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[Azure 宇宙 DB 中數據加密](./database-encryption-at-rest.md)的更多詳細資訊。
- 獲取[對Cosmos DB中數據的安全存取](secure-access-to-data.md)概述。
