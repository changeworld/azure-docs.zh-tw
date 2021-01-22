---
title: 使用 Azure Key Vault 和受控身分識別為您的 Azure Batch 帳戶設定客戶管理的金鑰
description: 瞭解如何使用金鑰加密批次資料
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 2ed19846209d098d9eba8dba991e08d1fc57f185
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678004"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>使用 Azure Key Vault 和受控身分識別為您的 Azure Batch 帳戶設定客戶管理的金鑰

根據預設，Azure Batch 會使用平臺管理的金鑰來加密 Azure Batch 服務中儲存的所有客戶資料，例如憑證、工作/工作中繼資料。 （選擇性）您可以使用您自己的金鑰（也就是客戶管理的金鑰）來加密 Azure Batch 中儲存的資料。

您提供的金鑰必須在 [Azure Key Vault](../key-vault/general/basic-concepts.md)中產生，而您想要使用客戶管理的金鑰來設定的 Batch 帳戶必須使用 [Azure 受控識別](../active-directory/managed-identities-azure-resources/overview.md)來啟用。

> [!IMPORTANT]
> Azure Batch 中客戶管理金鑰的支援目前處於西歐、北歐、瑞士北部、美國中部、美國中南部、美國中西部、美國東部、美國東部2、美國西部2、US Gov 維吉尼亞州和 US Gov 亞利桑那州區域的公開預覽版本。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>使用系統指派的受控識別建立 Batch 帳戶

### <a name="azure-portal"></a>Azure 入口網站

在 [Azure 入口網站](https://portal.azure.com/)中，當您建立 Batch 帳戶時，請在 [ **Advanced** ] 索引標籤下的 [識別類型] 中選取 [**系統指派**]。

![具有系統指派的身分識別類型的新 Batch 帳戶](./media/batch-customer-managed-key/create-batch-account.png)

建立帳戶之後，您可以在 [身分 **識別主體識別碼** ] 欄位中的 [ **屬性** ] 區段下找到唯一的 GUID。 身分 **識別類型** 將會顯示 `SystemAssigned` 。

![身分識別主體識別碼欄位中的唯一 GUID](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

當您建立新的 Batch 帳戶時，請 `SystemAssigned` 為 `--identity` 參數指定。

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

建立帳戶之後，您可以確認此帳戶已啟用系統指派的受控識別。 請務必記下 `PrincipalId` ，因為必須有此值，才能將 Key Vault 的存取權授與此 batch 帳戶。

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> 在 Batch 帳戶中建立的系統指派受控識別，只會用來從 Key Vault 取出客戶管理的金鑰。 Batch 集區上無法使用此身分識別。

## <a name="configure-your-azure-key-vault-instance"></a>設定您的 Azure Key Vault 執行個體

### <a name="create-an-azure-key-vault"></a>建立 Azure Key Vault

使用客戶管理的金鑰來建立 Azure Key Vault 實例以進行 Azure Batch 時，請確定已啟用虛 **刪除** 和 **清除保護** 。

![Key Vault 建立畫面](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>將存取原則新增至您的 Azure Key Vault 執行個體

在 Azure 入口網站中，建立 Key Vault 之後，在 [ **存取原則** ] 下的 [ **設定**] 下，新增使用受控識別的 Batch 帳戶存取權。 在 [ **金鑰許可權**] 下，選取 [ **取得**、 **包裝金鑰** 和解除包裝 **金鑰**]。 

![新增存取原則](./media/batch-customer-managed-key/key-permissions.png)

在 [**主體**] 下的 [**選取**] 欄位中，填入您先前抓取的 `principalId` ，或 batch 帳戶的名稱。

![輸入 principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>在 Azure Key Vault 中產生金鑰

在 Azure 入口網站中，移至 [ **金鑰** ] 區段中的 Key Vault 實例，然後選取 [ **產生/匯入**]。 請選取要作為的 **金鑰類型** `RSA` ，且 **RSA 金鑰大小** 至少為 `2048` 位。 `EC` 金鑰類型目前不支援做為 Batch 帳戶上客戶管理的金鑰。

![建立金鑰](./media/batch-customer-managed-key/create-key.png)

建立金鑰之後，按一下新建立的金鑰和目前的版本，並在 [**屬性**] 區段下複製 **金鑰識別碼**。  請確定在 [ **允許的作業**] 下，同時選取 [ **包裝金鑰** ] 和 [解除包裝 **金鑰** ]。

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>在 Azure Batch 帳戶上啟用客戶管理的金鑰

### <a name="azure-portal"></a>Azure 入口網站

在 [Azure 入口網站](https://portal.azure.com/)中，移至 [Batch 帳戶] 頁面。 在 [ **加密** ] 區段底下，啟用 [ **客戶管理的金鑰**]。 您可以直接使用金鑰識別碼，也可以選取金鑰保存庫，然後按一下 [ **選取金鑰保存庫和金鑰**]。

![在 [加密] 下，啟用客戶管理的金鑰](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

使用系統指派的受控識別建立 Batch 帳戶，並授與對 Key Vault 的存取權之後，請使用 [參數] 下的 URL 更新 Batch 帳戶 `{Key Identifier}` `keyVaultProperties` 。 也請將 **encryption_key_source** 設定為 `Microsoft.KeyVault` 。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>更新客戶管理的金鑰版本

當您建立新版本的金鑰時，請將 Batch 帳戶更新為使用新版本。 請遵循下列步驟：

1. 在 Azure 入口網站中流覽至您的 Batch 帳戶，並顯示加密設定。
2. 輸入新金鑰版本的 URI。 或者，您可以再次選取金鑰保存庫和金鑰，以更新版本。
3. 儲存您的變更。

您也可以使用 Azure CLI 來更新版本。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>使用不同的金鑰進行批次加密

若要變更批次加密所使用的金鑰，請遵循下列步驟：

1. 流覽至您的 Batch 帳戶，並顯示加密設定。
2. 輸入新金鑰的 URI。 或者，您也可以選取金鑰保存庫，然後選擇新的金鑰。
3. 儲存您的變更。

您也可以使用 Azure CLI 來使用不同的金鑰。

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>常見問題集
  * **現有 Batch 帳戶是否支援客戶管理的金鑰？** 不會。 只有新的 Batch 帳戶支援客戶管理的金鑰。
  * **我可以選取大於2048位的 RSA 金鑰大小嗎？** 是，也支援 RSA 金鑰大小 `3072` 和 `4096` 位。
  * **客戶管理的金鑰撤銷後，可以執行哪些作業？** 如果 Batch 無法存取客戶管理的金鑰，則唯一允許的作業是刪除帳戶。
  * **如果我不小心刪除了 Key Vault 金鑰，該如何還原 Batch 帳戶的存取權？** 由於已啟用清除保護和虛刪除，因此您可以還原現有的金鑰。 如需詳細資訊，請參閱 [復原 Azure Key Vault](../key-vault/general/key-vault-recovery.md)。
  * **是否可以停用客戶管理的金鑰？** 您可以隨時將 Batch 帳戶的加密類型設定回「Microsoft 受控金鑰」。 在此之後，您可以自由地刪除或變更金鑰。
  * **如何輪替我的金鑰？** 客戶管理的金鑰不會自動輪替。 若要輪替金鑰，請更新與帳戶相關聯的金鑰識別碼。
  * **在還原存取權之後，Batch 帳戶再次運作需要多久的時間？** 一旦還原存取權之後，最多可能需要10分鐘的時間才能存取帳戶。
  * **當 Batch 帳戶無法使用時，我的資源會發生什麼事？** 當 Batch 存取客戶管理的金鑰時，任何正在執行的集區將會繼續執行。 不過，節點將會轉換為無法使用的狀態，而且工作將會停止執行 (，並) 重新排入佇列。 一旦還原存取權，節點就會再次變成可用，而工作將會重新開機。
  * **此加密機制是否適用于 Batch 集區中的 VM 磁片？** 不會。 若為雲端服務設定集區，則不會對作業系統和暫存磁片套用任何加密。 針對虛擬機器設定集區，作業系統和任何指定的資料磁片預設會使用 Microsoft 平臺管理金鑰進行加密。 您目前無法為這些磁片指定自己的金鑰。 若要使用 Microsoft 平臺管理金鑰為 Batch 集區的 Vm 暫存磁片加密，您必須啟用[虛擬機器](/rest/api/batchservice/pool/add#virtualmachineconfiguration)設定集區中的[diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration)屬性。 針對高度敏感的環境，建議您啟用暫存磁片加密，並避免將機密資料儲存在 OS 和資料磁片上。 如需詳細資訊，請參閱[建立已啟用磁片加密的集](./disk-encryption.md)區
  * **Batch 帳戶上的系統指派受控識別是否可在計算節點上使用？** 不會。 此受控識別目前僅用來存取客戶管理金鑰的 Azure Key Vault。
