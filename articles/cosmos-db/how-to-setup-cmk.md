---
title: 為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
description: 了解如何使用 Azure Key Vault 為您的 Azure Cosmos DB 帳戶設定客戶管理的金鑰
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: thweiss
ms.openlocfilehash: 5629ddfe496ef1abd071ab579c885cbe1adeb344
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592082"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>使用 Azure Key Vault 為您的 Azure Cosmos 帳戶設定客戶管理的金鑰

儲存在 Azure Cosmos 帳戶中的資料，會使用由 Microsoft 管理的金鑰 (**服務管理的金鑰**) 自動進行縝密的加密。 (選擇性) 您可以選擇使用您所管理的金鑰 (**客戶管理的金鑰**) 來新增第二層加密。

![客戶資料周圍的加密層](./media/how-to-setup-cmk/cmk-intro.png)

您必須將客戶管理的金鑰儲存在 [Azure Key Vault](../key-vault/general/overview.md) 中，並且為客戶管理的金鑰已啟用的每個 Azure Cosmos 帳戶提供金鑰。 此金鑰會用來加密該帳戶中儲存的所有資料。

> [!NOTE]
> 目前，客戶管理的金鑰僅適用於新的 Azure Cosmos 帳戶。 您應在帳戶建立期間進行其設定。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>為您的 Azure 訂用帳戶註冊 Azure Cosmos DB 資源提供者

1. 登入 [Azure 入口網站](https://portal.azure.com/)，移至您的 Azure 訂用帳戶，然後在 [設定] 索引標籤下選取 [資源提供者]：

   ![左側功能表中的「資源提供者」項目](./media/how-to-setup-cmk/portal-rp.png)

1. 搜尋 **Microsoft.DocumentDB** 資源提供者。 確認資源提供者標示為已註冊。 如果不是，請選擇資源提供者，然後選取 [註冊]：

   ![註冊 Microsoft.DocumentDB 資源提供者](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>設定您的 Azure Key Vault 執行個體

若要將客戶管理的金鑰用於 Azure Cosmos DB，您必須在您打算用來裝載加密金鑰的 Azure Key Vault 執行個體上設定兩個屬性：**虛刪除**和**清除保護**。

如果您建立新的 Azure Key Vault 執行個體，請在建立期間啟用這些屬性：

![為新的 Azure Key Vault 執行個體啟用虛刪除和清除保護](./media/how-to-setup-cmk/portal-akv-prop.png)

如果您使用現有的 Azure Key Vault 執行個體，您可以查看 Azure 入口網站上的 [屬性] 區段，以確認這些屬性是否已啟用。 如果這些屬性皆未啟用，請參閱下列其中一篇文章中的「啟用虛刪除」和「啟用清除保護」小節：

- [透過 PowerShell 使用虛刪除](../key-vault/general/soft-delete-powershell.md)
- [如何透過 Azure CLI 使用虛刪除](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>將存取原則新增至您的 Azure Key Vault 執行個體

1. 在 Azure 入口網站中，移至您打算用來裝載加密金鑰的 Azure Key Vault 執行個體。 從左側功能表中選取 [存取原則]：

   ![左側功能表中的 [存取原則]](./media/how-to-setup-cmk/portal-akv-ap.png)

1. 選取 [+ 新增存取原則]。

1. 在 [金鑰權限] 下拉式功能表中，選取 [取得]、[將金鑰解除包裝] 和 [包裝金鑰] 權限：

   ![選取正確的權限](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. 在 [選取主體] 底下，選取 [未選取任何項目]。 然後，搜尋 **Azure Cosmos DB** 主體並加以選取 (您也可以依主體識別碼搜尋，以便找出：任何 Azure 區域的識別碼皆為 `a232010e-820c-4083-83bb-3ace5fc29d0b`，但 Azure Government 區域除外，其主體識別碼為 `57506a73-e302-42a9-b869-6f12d9ec29e9`)。 最後，選擇底部的 [選取]。 如果 **Azure Cosmos DB** 主體不在清單中，您可能必須重新註冊 **Microsoft.DocumentDB** 資源提供者，如本文的[註冊資源提供者](#register-resource-provider)一節所說明。

   ![選取 Azure Cosmos DB 主體](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. 選取 [新增] 以新增存取原則。

## <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中產生金鑰

1. 在 Azure 入口網站中，移至您打算用來裝載加密金鑰的 Azure Key Vault 執行個體。 然後，從左側功能表中選取 [金鑰]：

   ![左側功能表中的「金鑰」項目](./media/how-to-setup-cmk/portal-akv-keys.png)

1. 選取 [產生/匯入]、提供新金鑰的名稱，然後選取 RSA 金鑰大小。 建議至少要有 3072，以達到最佳安全性。 然後，選取 [建立]：

   ![建立新的金鑰](./media/how-to-setup-cmk/portal-akv-gen.png)

1. 在金鑰建立後，選取新建立的金鑰，然後選取其目前的版本。

1. 複製金鑰的 [金鑰識別碼]，但最後一個正斜線後面的部分除外：

   ![複製金鑰的金鑰識別碼](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>建立新的 Azure Cosmos 帳戶

### <a name="using-the-azure-portal"></a>使用 Azure 入口網站

當您從 Azure 入口網站建立新的 Azure Cosmos DB 帳戶時，請在 [加密] 步驟中選擇 [客戶管理的金鑰]。 在 [金鑰 URI] 欄位中，貼上您在先前的步驟中複製的 Azure Key Vault 金鑰的 URI/金鑰識別碼：

![在 Azure 入口網站中設定 CMK 參數](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a><a id="using-powershell"></a>使用 Azure PowerShell

當您使用 PowerShell 建立新的 Azure Cosmos DB 帳戶時：

- 在 **PropertyObject** 中，傳入您先前在 **keyVaultKeyUri** 屬性下複製的 Azure Key Vault 金鑰的 URI。

- 使用 **2019-12-12** 或更新版本作為 API 版本。

> [!IMPORTANT]
> 您必須明確設定 `locations` 屬性，才能使用客戶管理的金鑰成功建立帳戶。

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

建立帳戶之後，您可以擷取 Azure Key Vault 金鑰的 URI，藉以確認客戶管理的金鑰已啟用：

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>使用 Azure Resource Manager 範本

當您透過 Azure Resource Manager 範本建立新的 Azure Cosmos 帳戶時：

- 在**屬性**物件中，傳入您先前在 **keyVaultKeyUri** 屬性下複製的 Azure Key Vault 金鑰的 URI。

- 使用 **2019-12-12** 或更新版本作為 API 版本。

> [!IMPORTANT]
> 您必須明確設定 `locations` 屬性，才能使用客戶管理的金鑰成功建立帳戶。

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

使用下列 PowerShell 指令碼來部署範本：

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

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a>使用 Azure CLI

當您透過 Azure CLI 建立新的 Azure Cosmos 帳戶時，請傳入您先前在 `--key-uri` 參數下複製的 Azure Key Vault 金鑰的 URI。

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

建立帳戶之後，您可以擷取 Azure Key Vault 金鑰的 URI，藉以確認客戶管理的金鑰已啟用：

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>啟用客戶管理的金鑰是否會產生額外的費用？

否，啟用這項功能不需要任何費用。

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>客戶管理的金鑰對於容量規劃有何影響？

使用客戶管理的金鑰時，您的資料庫作業所使用的[要求單位](./request-units.md)將會增加，以反映執行資料加密和解密所需的額外處理。 這可能會導致您所佈建的容量使用率稍微提高。 請使用下表中的指引：

| 作業類型 | 要求單位增加 |
|---|---|
| 讀數 (依識別碼擷取項目) | 每項作業 + 5% |
| 任何寫入作業 | 每項作業 + 6%<br/>每個索引屬性大約 + 0.06 RU |
| 查詢、讀取變更摘要或衝突摘要 | 每項作業 + 15% |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>哪些資料會使用客戶管理的金鑰進行加密？

儲存在 Azure Cosmos 帳戶中的所有資料都會使用客戶管理的金鑰進行加密，但下列中繼資料除外：

- Azure Cosmos DB [帳戶、資料庫和容器](./account-overview.md#elements-in-an-azure-cosmos-account)的名稱

- [預存程序](./stored-procedures-triggers-udfs.md)的名稱

- 在[索引編製原則](./index-policy.md)中宣告的屬性路徑

- 容器的[分割區索引鍵](./partitioning-overview.md)的值

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>現有的 Azure Cosmos 帳戶是否支援客戶管理的金鑰？

這項功能目前僅適用於新帳戶。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>未來是否預計要支援比帳戶層級金鑰更精細的細微性？

目前無此計劃，但正在考量容器層級金鑰。

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>如何判斷我的 Azure Cosmos 帳戶是否已啟用客戶管理的金鑰？

您可以透過程式設計方式擷取 Azure Cosmos 帳戶的詳細資料，並確認 `keyVaultKeyUri` 屬性是否存在。 如需[在 PowerShell 中](#using-powershell)以及[使用 Azure CLI](#using-azure-cli) 執行該作業的方式，請參閱前述資訊。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>客戶管理的金鑰對備份有何影響？

Azure Cosmos DB 會對儲存在您帳戶中的資料進行[定期和自動備份](./online-backup-and-restore.md)。 此作業會備份已加密的資料。 若要使用還原的備份，則需要備份時所使用的加密金鑰。 這表示並未進行撤銷，且備份時所使用的金鑰版本仍會啟用。

### <a name="how-do-i-rotate-an-encryption-key"></a>如何輪替加密金鑰？

金鑰輪替的執行方式是在 Azure Key Vault 中建立新版本的金鑰：

![建立新的金鑰版本](./media/how-to-setup-cmk/portal-akv-rot.png)

先前的版本可在 24 小時後停用，或在 [Azure Key Vault 稽核記錄](../key-vault/general/logging.md)不再顯示 Azure Cosmos DB 中屬於該版本的活動之後停用。

### <a name="how-do-i-revoke-an-encryption-key"></a>如何撤銷加密金鑰？

金鑰撤銷可藉由停用最新版本的金鑰來完成：

![停用金鑰的版本](./media/how-to-setup-cmk/portal-akv-rev2.png)

或者，若要撤銷 Azure Key Vault 執行個體中的所有金鑰，您可以刪除為 Azure Cosmos DB 主體授與的存取原則：

![刪除 Azure Cosmos DB 主體的存取原則](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>客戶管理的金鑰撤銷後，可以執行哪些作業？

加密金鑰撤銷後，唯一可行的作業為刪除帳戶。

## <a name="next-steps"></a>後續步驟

- 深入了解 [Azure Cosmos DB 中的資料加密](./database-encryption-at-rest.md)。
- 取得[在 Cosmos DB 中安全存取資料](secure-access-to-data.md)的概觀。
