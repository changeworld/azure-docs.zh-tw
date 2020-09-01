---
title: 設定物件複寫 (預覽)
titleSuffix: Azure Storage
description: 了解如何設定物件複寫，以非同步方式將區塊 Blob 從儲存體帳戶中的容器複製到另一個容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9964a10b85d9f388a4498766c6e81ee52498aa37
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076169"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>設定區塊 Blob 的物件複寫 (預覽)

物件複寫 (預覽) 會以非同步方式複製來源儲存體帳戶與目的地帳戶之間的區塊 Blob。 如需物件複寫的詳細資訊，請參閱[物件複寫 (預覽)](object-replication-overview.md)。

當設定物件複寫時，您會建立複寫原則，其指定來源儲存體帳戶和目的地帳戶。 複寫原則包含一或多個規則，其指定來源容器和目的地容器，並指出將複寫來源容器中的哪些區塊 Blob。

本文描述如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來為儲存體帳戶設定物件複寫。 您也可以使用其中一個 Azure 儲存體資源提供者用戶端程式庫來設定物件複寫。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>建立複寫原則和規則

在您設定物件複寫之前，請先建立來源和目的地儲存體帳戶 (如果尚未建立)。 這兩個帳戶都必須為一般用途 v2 儲存體帳戶。 如需詳細資訊，請參閱[建立 Azure 儲存體帳戶](../common/storage-account-create.md)。

儲存體帳戶最多可作為兩個目的地帳戶的來源帳戶。 目的地帳戶可能不會有兩個以上的來源帳戶。 來源和目的地帳戶可能都在不同的區域中。 您可以設定個別的複寫原則，將資料複寫到每個目的地帳戶。

開始之前，請確定您已註冊下列功能預覽：

- [物件複寫 (預覽)](object-replication-overview.md)
- [Blob 版本設定 (預覽)](versioning-overview.md)
- [Azure Blob 儲存體中的變更摘要支援 (預覽)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

於 Azure 入口網站中設定物件複寫之前，請先在儲存體帳戶中各自建立來源和目的地容器 (如果尚未建立)。 此外，請在來源帳戶上啟用 Blob 版本設定和變更摘要，並在目的地帳戶上啟用 Blob 版本設定。

若要在 Azure 入口網站中建立複寫原則，請遵循下列步驟：

1. 巡覽至 Azure 入口網站的 [來源儲存體帳戶]。
1. 在 [ **Blob 服務**] 底下，選取 [ **物件**複寫]。
1. 選取 [ **設定複寫規則**]。
1. 選取 [目的地訂用帳戶] 和 [目的地儲存體帳戶]。
1. 在 [容器配對] 區段中，從來源帳戶中選取 [來源容器]，並從目的地帳戶中選取 [目的地容器]。 每個複寫原則最多可建立 10 組容器配對。

    下圖顯示一組複寫規則。

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

1. 如有需要，請指定一或多個篩選，以便僅複製符合前置詞模式的 Blob。 例如，如果指定前置詞 `b`，則只會複寫名稱開頭為該字母的 Blob。 您可指定虛擬目錄作為前置詞的一部分。 前置字元字串不支援萬用字元。

    下圖顯示篩選會限制哪些 Blob 符合複寫規則並接受複製。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="顯示複寫規則篩選的螢幕擷取畫面":::

1. 根據預設，複製範圍會設定為只複製新的物件。 若要複製容器中的所有物件，或從自訂日期和時間開始複製物件，請選取 [變更] 連結，並設定容器配對的複製範圍。

    下圖顯示自訂複製範圍。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="顯示物件複寫自訂複製範圍的螢幕擷取畫面":::

1. 選取 [儲存並套用] 以建立複寫原則，並開始複寫資料。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立複寫原則，請先安裝 [2.0.1](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) 版/preview 版或更新版本的 Az PowerShell 模組。 請遵循下列步驟安裝此預覽模組：

1. 使用 [設定] 下的 [應用程式與功能]，從 Windows 解除任何先前安裝的 Azure PowerShell。

1. 確定您已安裝最新版的 PowerShellGet。 開啟 Windows PowerShell 視窗，然後執行下列命令來安裝最新版本：

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    在安裝完 PowerShellGet 之後，關閉並重新開啟 PowerShell 視窗。

1. 安裝最新版的 Azure PowerShell：

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. 安裝 Az Storage 預覽模組：

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

如需如何安裝 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShellGet 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

下列範例說明如何在來源和目的地帳戶上建立複寫原則。 請記得以自有值來取代角括弧中的值：

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 建立複寫原則，請先安裝適用於 Azure 儲存體的預覽延伸模組：

```azurecli
az extension add -n storage-or-preview
```

接下來，使用 Azure 認證登入：

```azurecli
az login
```

在來源和目的地儲存體帳戶上啟用 Blob 版本設定，並在來源帳戶上啟用變更摘要。 請記得以自有值來取代角括弧中的值：

```azurecli
az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

在各自的儲存體帳戶中建立來源和目的地容器。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container4 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name source-container3 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name source-container4 \
    --auth-mode login
```

在目的地帳戶上建立新複寫原則及相關聯的規則。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

使用原則識別碼，在來源帳戶上建立原則。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>移除複寫原則

若要移除複寫原則及其相關聯的規則，請使用 Azure 入口網站、PowerShell 或 CLI。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站中移除複寫原則，請遵循下列步驟：

1. 巡覽至 Azure 入口網站的 [來源儲存體帳戶]。
1. 在 [設定] 下，選取 [物件複寫]。
1. 按一下原則名稱旁邊的 [更多] 按鈕。
1. 選取 [刪除規則] 。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要移除複寫原則，請同時刪除來源帳戶和目的地帳戶的原則。 刪除原則也會刪除與其建立關聯的所有規則。

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要移除複寫原則，請同時刪除來源帳戶和目的地帳戶的原則。 刪除原則也會刪除與其建立關聯的所有規則。

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>後續步驟

- [物件複寫概觀 (預覽)](object-replication-overview.md)
