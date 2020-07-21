---
title: 建立及管理加密範圍（預覽）
description: ''
services: storage
author: tamram
ms.service: storage
ms.date: 07/13/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f589f0108cf21e77be5103afcaa0242c6f191ab3
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531384"
---
# <a name="create-and-manage-encryption-scopes-preview"></a>建立及管理加密範圍（預覽）

加密範圍（預覽）可讓您管理個別 blob 或容器層級的加密。 加密範圍會隔離儲存體帳戶內安全記憶體保護區中的 blob 資料。 您可以使用加密範圍，在位於相同儲存體帳戶但屬於不同客戶的資料之間建立安全界限。 如需加密範圍的詳細資訊，請參閱[Blob 儲存體的加密範圍（預覽）](../common/storage-service-encryption.md#encryption-scopes-for-blob-storage-preview)。

本文說明如何建立加密範圍。 它也會說明如何在建立 blob 或容器時指定加密範圍。

## <a name="create-an-encryption-scope"></a>建立加密範圍

若要建立加密範圍，您必須先建立 Azure 金鑰保存庫，並新增您想要用於此範圍的金鑰。 金鑰保存庫必須同時啟用虛**刪除**和**清除保護**屬性，而且必須位於與儲存體帳戶相同的區域中。 如需詳細資訊，請參閱[使用客戶管理的金鑰搭配 Azure Key Vault 來管理 Azure 儲存體加密](../common/encryption-customer-managed-keys.md)。

加密範圍會在您建立時自動啟用。 建立加密範圍之後，您可以在建立 blob 時加以指定。 當您建立容器時，您也可以指定預設的加密範圍，這會自動套用至容器中的所有 blob。

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中建立加密範圍，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 選取 [**加密**] 設定。
1. 選取 [**加密範圍**] 索引標籤。
1. 按一下 [**新增**] 按鈕以新增新的加密範圍。
1. 在 [建立**加密範圍**] 窗格中，輸入新領域的名稱。
1. 選取加密類型，也就是**Microsoft 管理的金鑰**或**客戶管理的金鑰**。
    - 如果您選取了 [ **Microsoft 管理的金鑰**]，請按一下 [**建立**] 以建立加密範圍。
    - 如果您已選取 [**客戶管理的金鑰**]，請指定要用於此加密範圍的金鑰保存庫、金鑰和金鑰版本，如下圖所示。

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="顯示如何在 Azure 入口網站中建立加密範圍的螢幕擷取畫面":::

如需有關使用 Azure Key Vault Azure 儲存體加密來設定客戶管理金鑰的詳細資訊，請參閱[使用 Azure 入口網站以 Azure Key Vault 設定客戶管理的金鑰](../common/storage-encryption-keys-portal.md)。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立加密範圍，請先安裝 Az preview 模組版本。 建議使用最新的預覽版本，但在 1.13.4-preview 和更新版本中支援加密範圍。 移除任何其他版本的 Az. Storage 模組。

下列命令會安裝 Az. Storage [2.1.1-preview](https://www.powershellgallery.com/packages/Az.Storage/2.1.1-preview)模組：

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.1.1-preview -AllowPrerelease
```

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>建立受 Microsoft 管理的金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰所保護的新加密範圍，請使用參數呼叫**AzStorageEncryptionScope**命令 `-StorageEncryption` 。

請記得以您自己的值取代範例中的預留位置值：

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

若要建立以 Azure Key Vault 的客戶管理金鑰所保護的新加密範圍，請先為儲存體帳戶設定客戶管理的金鑰。 您必須將受控識別指派給儲存體帳戶，然後使用受控識別來設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 如需詳細資訊，請參閱[使用 PowerShell 設定具有 Azure Key Vault 的客戶管理金鑰](../common/storage-encryption-keys-powershell.md)。

若要設定客戶管理的金鑰以搭配加密範圍使用，您必須在金鑰保存庫上啟用「虛**刪除**」和「**清除保護**」屬性。 金鑰保存庫必須位於與儲存體帳戶相同的區域中。 如需詳細資訊，請參閱[使用客戶管理的金鑰搭配 Azure Key Vault 來管理 Azure 儲存體加密](../common/encryption-customer-managed-keys.md)。

請記得以您自己的值取代範例中的預留位置值：

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

接下來，使用參數呼叫**AzStorageEncryptionScope**命令 `-KeyvaultEncryption` ，並指定金鑰 URI。 請務必在金鑰 URI 上包含金鑰版本。 請記得以您自己的值取代範例中的預留位置值：

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -AccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI 建立加密範圍，請先安裝 Azure CLI 版本2.4.0 或更新版本。

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>建立受 Microsoft 管理的金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰所保護的新加密範圍，請呼叫[az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create)命令，並將 `--key-source` 參數指定為 `Microsoft.Storage` 。 請記得以您自己的值取代預留位置值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>建立受客戶管理金鑰保護的加密範圍

若要建立受 Microsoft 管理的金鑰所保護的新加密範圍，請呼叫[az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-create)命令，並將 `--key-source` 參數指定為 `Microsoft.Storage` 。 請記得以您自己的值取代預留位置值：

若要建立以 Azure Key Vault 的客戶管理金鑰所保護的新加密範圍，請先為儲存體帳戶設定客戶管理的金鑰。 您必須將受控識別指派給儲存體帳戶，然後使用受控識別來設定金鑰保存庫的存取原則，讓儲存體帳戶有權存取它。 如需詳細資訊，請參閱[使用 Azure CLI 設定 Azure Key Vault 的客戶管理金鑰](../common/storage-encryption-keys-cli.md)。

若要設定客戶管理的金鑰以搭配加密範圍使用，您必須在金鑰保存庫上啟用「虛**刪除**」和「**清除保護**」屬性。 金鑰保存庫必須位於與儲存體帳戶相同的區域中。 如需詳細資訊，請參閱[使用客戶管理的金鑰搭配 Azure Key Vault 來管理 Azure 儲存體加密](../common/encryption-customer-managed-keys.md)。

請記得以您自己的值取代範例中的預留位置值：

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

接下來，使用參數呼叫**az storage account encryption-scope create**命令 `--key-uri` ，並指定金鑰 URI。 請務必在金鑰 URI 上包含金鑰版本。 請記得以您自己的值取代範例中的預留位置值：

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="list-encryption-scopes-for-storage-account"></a>列出儲存體帳戶的加密範圍

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中查看儲存體帳戶的加密範圍，請流覽至儲存體帳戶的 [**加密範圍**] 設定。 在此窗格中，您可以啟用或停用加密範圍，或變更加密範圍的金鑰。

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="螢幕擷取畫面，顯示中的加密範圍清單 Azure 入口網站":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 列出適用于儲存體帳戶的加密範圍，請呼叫 AzStorageEncryptionScope 命令。 請記得以您自己的值取代範例中的預留位置值：

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

若要依儲存體帳戶列出資源群組中的所有加密範圍，請使用管線語法，如下所示：

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要列出具有 Azure CLI 的儲存體帳戶可用的加密範圍，請呼叫[az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-list)命令。 請記得以您自己的值取代範例中的預留位置值：

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>建立具有預設加密範圍的容器

# <a name="portal"></a>[入口網站](#tab/portal)

若要在 Azure 入口網站中建立具有預設加密範圍的容器，請先建立加密範圍，如[建立加密領域](#create-an-encryption-scope)中所述。 接下來，請遵循下列步驟來建立容器：

1. 流覽至儲存體帳戶中的容器清單，然後選取 [**新增**] 按鈕以建立新的容器。
1. 在 [**新增容器**] 窗格中，展開 [ **Advanced** ] 設定。
1. 在 [**加密範圍**] 下拉式選單中，選取容器的預設加密範圍。
1. 若要要求容器中的所有 blob 都使用預設的加密範圍，請選取此核取方塊，以**針對容器中的所有 Blob 使用此加密範圍**。 如果選取此核取方塊，容器中的個別 blob 就無法覆寫預設的加密範圍。

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="顯示具有預設加密範圍之容器的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立具有預設加密範圍的容器，請呼叫[AzRmStorageContainer](/powershell/module/az.storage/new-azrmstoragecontainer)命令，並指定參數的範圍 `-DefaultEncryptionScope` 。 **AzRmStorageContainer**命令會使用 Azure 儲存體資源提供者來建立容器，這會啟用加密範圍和其他資源管理作業的設定。

您可以使用自己的加密範圍來建立個別 blob，除非已將容器設定為要求所有 blob 都使用其預設範圍。 若要強制容器中的所有 blob 使用容器的預設範圍，請將 `-PreventEncryptionScopeOverride` 參數設定為 `true` 。

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

若要使用 Azure CLI 的預設加密範圍來建立容器，請呼叫[az storage container create](/cli/azure/storage/container#az-storage-container-create)命令，並指定參數的範圍 `--default-encryption-scope` 。 您可以使用自己的加密範圍來建立個別 blob，除非已將容器設定為要求所有 blob 都使用其預設範圍。 若要強制容器中的所有 blob 使用容器的預設範圍，請將 `--prevent-encryption-scope-override` 參數設定為 `true` 。

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

如果用戶端在將 blob 上傳至具有預設加密範圍的容器時嘗試指定範圍，且容器已設定為防止 blob 覆寫預設範圍，則作業會失敗，並顯示一則訊息，指出容器加密原則禁止該要求。

## <a name="upload-a-blob-with-an-encryption-scope"></a>上傳具有加密範圍的 blob

當您上傳 blob 時，您可以指定該 blob 的加密範圍，或使用容器的預設加密範圍（若已指定）。 

# <a name="portal"></a>[入口網站](#tab/portal)

若要上傳具有 Azure 入口網站中指定之加密範圍的 blob，請先建立加密範圍，如[建立加密領域](#create-an-encryption-scope)中所述。 接下來，請遵循下列步驟來建立 blob：

1. 流覽至您要上傳 blob 的容器。
1. 選取 [**上傳**] 按鈕，然後找出要上傳的 blob。
1. 在 [**上傳 blob** ] 窗格中，展開 [ **Advanced** ] 設定。
1. 找出 [**加密範圍**] 下拉式區段。 根據預設，會使用容器的預設加密範圍來建立 blob （若已指定）。 如果容器要求 blob 使用預設的加密範圍，則會停用此區段。
1. 若要為您上傳的 blob 指定不同的範圍，請選取 **[選擇現有的範圍**]，然後從下拉式選單選取所需的範圍。

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="顯示如何上傳具有加密範圍之 blob 的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要上傳具有使用 PowerShell 指定之加密範圍的 blob，請呼叫[get-azstorageblobcontent](/powershell/module/az.storage/set-azstorageblobcontent)命令，並提供 blob 的加密範圍。

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

若要上傳具有使用 Azure CLI 所指定之加密範圍的 blob，請呼叫[az storage blob upload](/cli/azure/storage/blob#az-storage-blob-upload)命令，並提供 blob 的加密範圍。

如果您使用 Azure Cloud Shell，請依照[上傳 blob](storage-quickstart-blobs-cli.md#upload-a-blob)中所述的步驟，在根目錄中建立檔案。 接著，您可以使用下列範例，將此檔案上傳至 blob。

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

# <a name="portal"></a>[入口網站](#tab/portal)

若要變更保護 Azure 入口網站中範圍的金鑰，請遵循下列步驟：

1. 流覽至 [**加密範圍**] 索引標籤，以查看儲存體帳戶的加密範圍清單。
1. 選取您想要修改之範圍旁的 [**更多**] 按鈕。
1. 在 [**編輯加密範圍**] 窗格中，您可以將加密類型從 Microsoft 管理的金鑰變更為客戶管理的金鑰，反之亦然。
1. 若要選取新的客戶管理金鑰，請選取 [**使用新的金鑰**]，並指定金鑰保存庫、金鑰和金鑰版本。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 將保護加密範圍的金鑰從客戶管理的金鑰變更為 Microsoft 管理的金鑰，請呼叫**AzStorageEncryptionScope**命令，並傳入 `-StorageEncryption` 參數：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

若要將保護加密範圍的金鑰從 Microsoft 管理的金鑰變更為客戶管理的金鑰，請先確定您已使用儲存體帳戶的 Azure Key Vault 啟用客戶管理的金鑰。 如需詳細資訊，請參閱[使用 PowerShell 設定具有 Azure Key Vault 的客戶管理金鑰](../common/storage-encryption-keys-powershell.md)。 接下來，呼叫**AzStorageEncryptionScope**命令，並傳入 `-KeyUri` 和 `-KeyvaultEncryption` 參數：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要使用 Azure CLI，將保護加密範圍的金鑰從客戶管理的金鑰變更為 Microsoft 管理的金鑰，請呼叫[az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update)命令，並傳入 `--key-source` 具有下列值的參數 `Microsoft.Storage` ：

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

若要將保護加密範圍的金鑰從 Microsoft 管理的金鑰變更為客戶管理的金鑰，請先確定您已使用儲存體帳戶的 Azure Key Vault 啟用客戶管理的金鑰。 如需詳細資訊，請參閱[使用 Azure CLI 設定 Azure Key Vault 的客戶管理金鑰](../common/storage-encryption-keys-cli.md)。 接下來，呼叫**az storage account encryption-scope update**命令，傳入 `--key-uri` 參數，並傳入 `--key-source` 具有下列值的參數 `Microsoft.KeyVault` ：

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

# <a name="portal"></a>[入口網站](#tab/portal)

若要停用 Azure 入口網站中的加密範圍，請流覽至儲存體帳戶的 [**加密**範圍] 設定，選取所需的加密範圍，然後選取 [**停**用]。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 停用加密範圍，請呼叫 AzStorageEncryptionScope 命令，並包含 `-State` 值為的參數 `disabled` ，如下列範例所示。 若要重新啟用加密範圍，請呼叫相同的命令，並將 `-State` 參數設定為 `enabled` 。 請記得以您自己的值取代範例中的預留位置值：

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

若要停用 Azure CLI 的加密範圍，請呼叫[az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az-storage-account-encryption-scope-update)命令，並包含 `--state` 值為的參數 `Disabled` ，如下列範例所示。 若要重新啟用加密範圍，請呼叫相同的命令，並將 `--state` 參數設定為 `Enabled` 。 請記得以您自己的值取代範例中的預留位置值：

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
- [搭配 Azure Key Vault 使用客戶管理的金鑰來管理 Azure 儲存體加密](../common/encryption-customer-managed-keys.md)
