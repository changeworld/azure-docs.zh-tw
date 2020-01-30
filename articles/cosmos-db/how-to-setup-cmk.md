---
title: 為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
description: 瞭解如何使用 Azure Key Vault 為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 7a77a54dc59ec427bf6abdf8fc1d410533b5be44
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2020
ms.locfileid: "76771891"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure Key Vault 為您的 Azure Cosmos 帳戶設定客戶管理的金鑰

> [!NOTE]
> 此時，您必須要求存取權才能使用這項功能。 若要這麼做，請洽詢[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)。

儲存在您的 Azure Cosmos 帳戶中的資料會自動且順暢地加密。 Azure Cosmos DB 提供兩個選項來管理用來加密待用資料的金鑰：

- **服務管理的金鑰**-根據預設，Microsoft 會管理用來加密 Azure Cosmos 帳戶中資料的金鑰。

- **客戶管理的金鑰（CMK）** -您可以選擇使用您自己的金鑰來新增第二層加密。

您必須將客戶管理的金鑰儲存在[Azure Key Vault](../key-vault/key-vault-overview.md)中，並為使用客戶管理的金鑰啟用的每個 Azure Cosmos 帳戶提供金鑰。 此金鑰是用來加密該帳戶中儲存的所有資料。

> [!NOTE]
> 目前，客戶管理的金鑰僅適用于新的 Azure Cosmos 帳戶，您應該在帳戶建立期間進行設定。

## <a id="register-resource-provider"></a>為您的 Azure 訂用帳戶註冊 Azure Cosmos DB 資源提供者

1. 登入[Azure 入口網站](https://portal.azure.com/)，移至您的 Azure 訂用帳戶，然後在 [**設定**] 索引標籤下選取**資源提供者**：

   ![左側功能表中的 [資源提供者] 專案](./media/how-to-setup-cmk/portal-rp.png)

1. 搜尋**Microsoft DocumentDB**資源提供者。 確認資源提供者是否已標示為已註冊。 如果不是，請選擇資源提供者，然後選取 [**註冊**]：

   ![正在註冊 Microsoft DocumentDB 資源提供者](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>設定您的 Azure Key Vault 實例

若要使用客戶管理的金鑰搭配 Azure Cosmos DB，您必須在您打算用來裝載加密金鑰的 Azure Key Vault 實例上設定兩個屬性。 這些屬性包括「虛**刪除**」和「不要**清除**」。 這些屬性預設不會啟用，您可以使用 PowerShell 或 Azure CLI 來啟用它們。

若要瞭解如何在現有的 Azure Key Vault 實例上啟用這些屬性，請參閱下列其中一篇文章中的「啟用虛刪除」和「啟用清除保護」章節：

- [如何使用 PowerShell 進行虛刪除](../key-vault/key-vault-soft-delete-powershell.md)
- [如何搭配 Azure CLI 使用虛刪除](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>將存取原則新增至您的 Azure Key Vault 實例

1. 從 Azure 入口網站，移至您打算用來裝載加密金鑰的 Azure Key Vault 實例。 從左側功能表中選取 [**存取原則**]：

   ![左側功能表中的 [存取原則]](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 選取 [ **+ 新增存取原則**]

1. 在 [**金鑰許可權**] 下拉式功能表中，選取 [**取得**]、[解除包裝**金鑰**] 和 [**包裝金鑰**許可權]：

   ![選取正確的許可權](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在 [**選取主體**] 底下，選取 [**未選取**]。 然後，搜尋**Azure Cosmos DB**主體並加以選取。 最後，按一下底部的 [**選取**] （如果**Azure Cosmos DB**主體不在清單中，您可能需要重新註冊**Microsoft DocumentDB**資源提供者，如本文的[註冊資源提供者](#register-resource-provider)一節所述）：

   ![選取 Azure Cosmos DB 主體](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 選取 **[新增]** 以新增新的存取原則

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中產生金鑰

1. 從 Azure 入口網站，移至您打算用來裝載加密金鑰的 Azure Key Vault 實例。 然後，從左側功能表中選取 [**金鑰**]：

   ![左側功能表中的 [金鑰] 專案](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 選取 [**產生/匯入**]，提供新金鑰的名稱，選取 [RSA 金鑰大小] （最少為3072，建議使用 [最佳安全性]），然後選取 [**建立**]：

   ![建立新的金鑰](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 建立金鑰之後，請選取新建立的金鑰，然後選取其目前的版本。

1. 複製金鑰的**金鑰識別碼**，但最後一個正斜線後面的部分除外：

   ![複製金鑰的金鑰識別碼](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>建立新的 Azure Cosmos 帳戶

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

從 Azure 入口網站建立新的 Azure Cosmos DB 帳戶時，請在**加密**步驟中選擇 [**客戶管理的金鑰**]。 在 [**金鑰 URI** ] 欄位中，貼上您從上一個步驟複製之 Azure Key Vault 金鑰的 URI/金鑰識別碼：

![在 Azure 入口網站中設定 CMK 參數](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>使用 Azure PowerShell

使用 PowerShell 建立新的 Azure Cosmos DB 帳戶時，

- 傳遞從先前複製的 Azure Key Vault 金鑰的 URI，其位於**PropertyObject**中的**keyVaultKeyUri**屬性底下。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須明確地設定 `Location` 參數，才能使用客戶管理的金鑰成功建立帳戶。

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

### <a name="using-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

透過 Azure Resource Manager 範本建立新的 Azure Cosmos 帳戶時：

- 傳遞您稍早在**properties**物件的**keyVaultKeyUri**屬性下複製的 Azure Key Vault 金鑰的 URI。

- 使用**2019-12-12**作為 API 版本。

> [!IMPORTANT]
> 您必須明確地設定 `Location` 參數，才能使用客戶管理的金鑰成功建立帳戶。

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

使用下列 PowerShell 腳本部署範本：

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

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>使用客戶管理的金鑰時，是否會有任何額外的費用？

可以。 若要考慮使用客戶管理的金鑰來管理資料加密和解密所需的額外計算負載，針對 Azure Cosmos 帳戶執行的所有作業都會在[要求單位](./request-units.md)中耗用25% 的增加。

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些資料會使用客戶管理的金鑰進行加密？

您的 Azure Cosmos 帳戶中儲存的所有資料都會使用客戶管理的金鑰進行加密，但下列中繼資料除外：

- Azure Cosmos DB[帳戶、資料庫和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名稱

- [預存程式](./stored-procedures-triggers-udfs.md)的名稱

- 在[索引編制原則](./index-policy.md)中宣告的屬性路徑

- 容器的分割區索引[鍵](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>現有的 Azure Cosmos 帳戶是否支援客戶管理的金鑰？

這項功能目前僅適用于新帳戶。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>是否有計劃支援比帳戶層級金鑰更精細的資料細微性？

但目前不會考慮容器層級的索引鍵。

### <a name="how-does-customer-managed-keys-affect-a-backup"></a>客戶管理的金鑰如何影響備份？

Azure Cosmos DB 會針對儲存在您帳戶中的資料[進行定期和自動備份](./online-backup-and-restore.md)。 這種作業會備份加密的資料。 若要使用還原的備份，則需要備份時所使用的加密金鑰。 這表示不應該進行撤銷，而且備份時所使用的金鑰版本仍應啟用。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何? 撤銷加密金鑰？

金鑰撤銷是藉由停用最新版本的金鑰來完成：

![停用金鑰的版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要從 Azure Key Vault 實例中撤銷所有金鑰，您可以刪除授與 Azure Cosmos DB 主體的存取原則：

![正在刪除 Azure Cosmos DB 主體的存取原則](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>撤銷客戶管理的金鑰後，可以使用哪些作業？

撤銷加密金鑰時唯一可能的作業是刪除帳戶。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Azure Cosmos DB 中的資料加密](./database-encryption-at-rest.md)
- 深入瞭解[Cosmos DB 中資料的安全存取](secure-access-to-data.md)