---
title: '建立及管理加密範圍 (預覽) '
description: 瞭解如何建立加密範圍，以隔離容器或 blob 層級的 blob 資料。
services: storage
author: tamram
ms.service: storage
ms.date: 09/17/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 9210c54305427c82d5666d68573fd3af41e8cef7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90972188"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>建立及管理加密範圍 (預覽) 

加密範圍 (預覽版) 可讓您管理個別 blob 或容器層級的加密。 加密範圍會隔離儲存體帳戶內安全記憶體保護區中的 Blob 資料。 您可以使用加密範圍來建立位於相同儲存體帳戶但屬於不同客戶的資料之間的安全界限。 如需加密範圍的詳細資訊，請參閱 [Blob 儲存體 (預覽) 的加密範圍 ](encryption-scope-overview.md)。

本文說明如何建立加密範圍。 它也會顯示當您建立 blob 或容器時，如何指定加密範圍。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-an-encryption-scope"></a>建立加密範圍

您可以使用 Microsoft 管理的金鑰或以客戶管理的金鑰（儲存在 Azure Key Vault 或 Azure Key Vault 受控硬體安全性模型 (HSM)  (preview) ）來建立加密範圍。 若要使用客戶管理的金鑰建立加密範圍，您必須先建立金鑰保存庫或受控 HSM，並新增您想要用於領域的金鑰。 Key vault 或受控 HSM 必須啟用清除保護，而且必須與儲存體帳戶位於相同的區域中。

加密範圍會在您建立時自動啟用。 建立加密範圍之後，您可以在建立 blob 時指定它。 您也可以在建立容器時指定預設加密範圍，此容器會自動套用到容器中的所有 blob。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中建立加密範圍，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選取 [ **加密** ] 設定。
1. 選取 [ **加密範圍** ] 索引標籤。
1. 按一下 [ **新增** ] 按鈕，以新增新的加密範圍。
1. 在 [建立 **加密範圍** ] 窗格中，輸入新領域的名稱。
1. 選取加密類型，也就是 **由 Microsoft 管理的金鑰** 或 **由客戶管理的金鑰**。
    - 如果您選取了 **Microsoft 管理的金鑰**，請按一下 [ **建立** ] 以建立加密範圍。
    - 如果您選取了 **客戶管理的金鑰**，請指定要用於此加密範圍的金鑰保存庫或受控 HSM、金鑰和金鑰版本，如下圖所示。

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="顯示如何在 Azure 入口網站中建立加密範圍的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立加密範圍，請先安裝 Az. Storage preview 模組版本。 建議使用最新的預覽版本，但1.13.4 預覽版和更新版本支援加密範圍。 移除任何其他版本的 Az. Storage 模組。

下列命令會安裝 Az. Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview) 模組：

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>建立受 Microsoft 管理的金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰保護的新加密範圍，請使用參數呼叫 **AzStorageEncryptionScope** 命令 `-StorageEncryption` 。

請記得將範例中的預留位置值取代為您自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>建立受客戶管理金鑰保護的加密範圍

若要建立受客戶管理金鑰（儲存在金鑰保存庫或受控 HSM）保護的新加密範圍，請先為儲存體帳戶設定客戶管理的金鑰。 您必須將受控識別指派給儲存體帳戶，然後使用受控識別來設定金鑰保存庫或受控 HSM 的存取原則，讓儲存體帳戶有權存取它。

若要設定客戶管理的金鑰以用於加密範圍，必須在金鑰保存庫或受控 HSM 上啟用清除保護。 Key vault 或受控 HSM 必須位於與儲存體帳戶相同的區域中。

請記得將範例中的預留位置值取代為您自己的值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri-with-version>"
$scopeName2 = "customer2scope"


# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

接下來，使用參數呼叫 **新的-AzStorageEncryptionScope** 命令 `-KeyvaultEncryption` ，並指定金鑰 URI。 請務必在金鑰 URI 上包含金鑰版本。 請記得將範例中的預留位置值取代為您自己的值：

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 建立加密範圍，請先安裝 Azure CLI 2.4.0 版或更新版本。

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>建立受 Microsoft 管理的金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰保護的新加密範圍，請呼叫 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 命令，將參數指定 `--key-source` 為 `Microsoft.Storage` 。 請記得以您自己的值取代預留位置值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>建立受客戶管理金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰保護的新加密範圍，請呼叫 [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create) 命令，將參數指定 `--key-source` 為 `Microsoft.Storage` 。 請記得以您自己的值取代預留位置值：

若要在金鑰保存庫或受控 HSM 中建立受客戶管理金鑰保護的新加密範圍，請先為儲存體帳戶設定客戶管理的金鑰。 您必須將受控識別指派給儲存體帳戶，然後使用受控識別來設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取該金鑰保存庫。 如需詳細資訊，請參閱[適用於 Azure 儲存體加密的客戶自控金鑰](../common/customer-managed-keys-overview.md)。

若要設定客戶管理的金鑰以用於加密範圍，必須在金鑰保存庫或受控 HSM 上啟用清除保護。 Key vault 或受控 HSM 必須位於與儲存體帳戶相同的區域中。

請記得將範例中的預留位置值取代為您自己的值：

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

接下來，使用參數呼叫 **az storage account encryption scope create** 命令 `--key-uri` ，並指定金鑰 URI。 請務必在金鑰 URI 上包含金鑰版本。 請記得將範例中的預留位置值取代為您自己的值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

若要瞭解如何使用金鑰保存庫中客戶管理的金鑰來設定 Azure 儲存體加密，請參閱 [使用儲存在 Azure Key Vault 中之客戶管理的金鑰來設定加密](../common/customer-managed-keys-configure-key-vault.md)。 若要在受管理的 HSM 中設定客戶管理的金鑰，請參閱 [使用儲存在 Azure Key Vault 受控 hsm (preview) 中的客戶管理金鑰來設定加密 ](../common/customer-managed-keys-configure-key-vault-hsm.md)。

## <a name="list-encryption-scopes-for-storage-account"></a>列出儲存體帳戶的加密範圍

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中查看儲存體帳戶的加密範圍，請流覽至儲存體帳戶的 **加密範圍** 設定。 從這個窗格中，您可以啟用或停用加密範圍，或變更加密範圍的金鑰。

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="顯示如何在 Azure 入口網站中建立加密範圍的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 列出適用于儲存體帳戶的加密範圍，請呼叫 **AzStorageEncryptionScope** 命令。 請記得將範例中的預留位置值取代為您自己的值：

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

若要依儲存體帳戶列出資源群組中的所有加密範圍，請使用管線語法：

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要列出具有 Azure CLI 的儲存體帳戶可用的加密範圍，請呼叫 [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list) 命令。 請記得將範例中的預留位置值取代為您自己的值：

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>建立具有預設加密範圍的容器

當您建立容器時，可以指定預設的加密範圍。 該容器中的 blob 預設會使用該範圍。

您可以使用自己的加密範圍來建立個別的 blob，除非容器設定為要求所有 blob 使用其預設範圍。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中建立具有預設加密範圍的容器，請先建立加密範圍，如 [建立加密範圍](#create-an-encryption-scope)中所述。 接下來，請遵循下列步驟來建立容器：

1. 流覽至儲存體帳戶中的容器清單，然後選取 [ **新增** ] 按鈕以建立新的容器。
1. 在 [**新增容器**] 窗格中，展開 [ **Advanced** settings]。
1. 在 [ **加密範圍** ] 下拉式清單中，選取容器的預設加密範圍。
1. 若要要求容器中的所有 blob 使用預設加密範圍，請選取核取方塊，將 **此加密範圍用於容器中的所有 blob**。 如果選取此核取方塊，則容器中的個別 blob 無法覆寫預設的加密範圍。

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="顯示如何在 Azure 入口網站中建立加密範圍的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立具有預設加密範圍的容器，請呼叫 [AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer) 命令，並指定參數的範圍 `-DefaultEncryptionScope` 。 **AzRmStorageContainer**命令會使用 Azure 儲存體資源提供者來建立容器，以啟用加密範圍和其他資源管理作業的設定。

若要強制容器中的所有 blob 使用容器的預設範圍，請將 `-PreventEncryptionScopeOverride` 參數設定為 `true` 。

```powershell
$containerName1 = "container1"
$containerName2 = "container2"

# Create a container with a default encryption scope that cannot be overridden.
New-AzRmStorageContainer -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Name $containerName1 `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要建立具有 Azure CLI 之預設加密範圍的容器，請呼叫 [az storage container create](/cli/azure/storage/container#az-storage-container-create) 命令，並指定參數的範圍 `--default-encryption-scope` 。 若要強制容器中的所有 blob 使用容器的預設範圍，請將 `--prevent-encryption-scope-override` 參數設定為 `true` 。

下列範例會使用您的 Azure AD 帳戶來授權作業，以便建立容器。 您也可以使用帳戶存取金鑰。 如需詳細資訊，請參閱[使用 Azure CLI 授與 Blob 或佇列資料的存取權](../common/authorize-data-operations-cli.md)。

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

如果用戶端在將 blob 上傳至具有預設加密範圍的容器時嘗試指定範圍，且容器設定為防止 blob 覆寫預設範圍，則作業會失敗，並顯示一則訊息，指出容器加密原則禁止此要求。

## <a name="upload-a-blob-with-an-encryption-scope"></a>使用加密範圍上傳 blob

當您上傳 blob 時，您可以指定該 blob 的加密範圍，或使用容器的預設加密範圍（如果已指定）。

# <a name="portal"></a>[入口網站](#tab/portal)

若要使用 Azure 入口網站中指定的加密範圍上傳 blob，請先建立加密範圍，如 [建立加密範圍](#create-an-encryption-scope)中所述。 接下來，請遵循下列步驟來建立 blob：

1. 流覽至您要上傳 blob 的容器。
1. 選取 [ **上傳** ] 按鈕，然後找出要上傳的 blob。
1. 在 [**上傳 blob** ] 窗格中，展開 [ **Advanced** settings]。
1. 找出 [ **加密範圍** ] 下拉式區段。 根據預設，會使用容器的預設加密範圍來建立 blob （如果已指定）。 如果容器要求 blob 使用預設加密範圍，則會停用此區段。
1. 若要為您要上傳的 blob 指定不同的範圍，請選取 **[選擇現有的範圍**]，然後從下拉式清單中選取所需的範圍。

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="顯示如何在 Azure 入口網站中建立加密範圍的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 以指定的加密範圍上傳 blob，請呼叫 [>get-azstorageblobcontent](/powershell/module/az.storage/set-azstorageblobcontent) 命令，並提供 blob 的加密範圍。

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = (Get-AzStorageAccount -ResourceGroupName $rgName -StorageAccountName $accountName).Context

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx
# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx -Container $containerName2 -File $localSrcFile -Blob "helloworld.txt" -BlobType Block -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 指定的加密範圍上傳 blob，請呼叫 [az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload) 命令，並提供 blob 的加密範圍。

如果您使用 Azure Cloud Shell，請依照 [上傳 blob](storage-quickstart-blobs-cli.md#upload-a-blob) 中所述的步驟，在根目錄中建立檔案。 然後，您可以使用下列範例，將此檔案上傳至 blob。

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>變更範圍的加密金鑰

若要將保護加密範圍的金鑰從 Microsoft 管理的金鑰變更為客戶管理的金鑰，請先確定您已為儲存體帳戶使用 Azure Key Vault 或 Key Vault HSM 啟用客戶管理的金鑰。 如需詳細資訊，請參閱 [使用儲存在 Azure Key Vault 中的客戶管理金鑰來設定加密](../common/customer-managed-keys-configure-key-vault.md) ，或 [使用儲存在 Azure Key Vault 中客戶管理的金鑰來設定加密](../common/customer-managed-keys-configure-key-vault.md)。

# <a name="portal"></a>[入口網站](#tab/portal)

若要變更保護 Azure 入口網站範圍的金鑰，請遵循下列步驟：

1. 流覽至 [ **加密範圍** ] 索引標籤，以查看儲存體帳戶的加密範圍清單。
1. 選取您要修改範圍旁的 [ **更多** ] 按鈕。
1. 在 [ **編輯加密範圍** ] 窗格中，您可以將加密類型從 Microsoft 管理的金鑰變更為客戶管理的金鑰，反之亦然。
1. 若要選取新的客戶管理金鑰，請選取 [ **使用新的金鑰** ]，並指定金鑰保存庫、金鑰和金鑰版本。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 將保護加密範圍的金鑰從客戶管理的金鑰變更為 Microsoft 管理的金鑰，請呼叫 **AzStorageEncryptionScope** 命令並傳入 `-StorageEncryption` 參數：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

接下來，呼叫 **AzStorageEncryptionScope** 命令並傳入 `-KeyUri` 和 `-KeyvaultEncryption` 參數：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 將保護加密範圍的金鑰從客戶管理的金鑰變更為受 Microsoft 管理的金鑰，請呼叫 [az storage account encryption scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 命令，並傳入 `--key-source` 參數值 `Microsoft.Storage` ：

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

接下來，呼叫 **az storage account encryption scope update** 命令，傳入 `--key-uri` 參數，並傳入 `--key-source` 參數值 `Microsoft.KeyVault` ：

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>停用加密範圍

當加密範圍停用時，就不會再向您收費。 停用任何不需要的加密範圍，以避免不必要的費用。 如需詳細資訊，請參閱[待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中停用加密範圍，請流覽至儲存體帳戶的 **加密** 範圍設定，並選取所需的加密範圍，然後選取 [ **停**用]。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 停用加密範圍，請呼叫 Update-AzStorageEncryptionScope 命令，並包含 `-State` 具有值的參數 `disabled` ，如下列範例所示。 若要重新啟用加密範圍，請呼叫相同的命令，並將 `-State` 參數設定為 `enabled` 。 請記得將範例中的預留位置值取代為您自己的值：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 停用加密範圍，請呼叫 [az storage account encryption scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update) 命令，並包含 `--state` 具有值的參數 `Disabled` ，如下列範例所示。 若要重新啟用加密範圍，請呼叫相同的命令，並將 `--state` 參數設定為 `Enabled` 。 請記得將範例中的預留位置值取代為您自己的值：

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

---

## <a name="next-steps"></a>後續步驟

- [待用資料的 Azure 儲存體加密](../common/storage-service-encryption.md)
- [Blob 儲存體 (預覽) 的加密範圍 ](encryption-scope-overview.md)
- [Azure 儲存體加密的客戶管理金鑰](../common/customer-managed-keys-overview.md)