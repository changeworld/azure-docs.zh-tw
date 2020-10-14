---
title: 設定物件複寫
titleSuffix: Azure Storage
description: 了解如何設定物件複寫，以非同步方式將區塊 Blob 從儲存體帳戶中的容器複製到另一個容器。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: bca960100ee0c9d7e2a779dc86030fc59949dca5
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92055965"
---
# <a name="configure-object-replication-for-block-blobs"></a>設定區塊 blob 的物件複寫

物件複寫會以非同步方式複製來源儲存體帳戶和目的地帳戶之間的區塊 blob。 如需有關物件複寫的詳細資訊，請參閱 [物件](object-replication-overview.md)複寫。

當設定物件複寫時，您會建立複寫原則，其指定來源儲存體帳戶和目的地帳戶。 複寫原則包含一或多個規則，其指定來源容器和目的地容器，並指出將複寫來源容器中的哪些區塊 Blob。

本文描述如何使用 Azure 入口網站、PowerShell 或 Azure CLI 來為儲存體帳戶設定物件複寫。 您也可以使用其中一個 Azure 儲存體資源提供者用戶端程式庫來設定物件複寫。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>建立複寫原則和規則

在您設定物件複寫之前，請先建立來源和目的地儲存體帳戶 (如果尚未建立)。 這兩個帳戶都必須為一般用途 v2 儲存體帳戶。 如需詳細資訊，請參閱[建立 Azure 儲存體帳戶](../common/storage-account-create.md)。

物件複寫需要為來源和目的地帳戶啟用 blob 版本設定，而且已為來源帳戶啟用 blob 變更摘要。 若要深入瞭解 blob 版本設定，請參閱 [blob 版本](versioning-overview.md)設定。 若要深入瞭解變更摘要，請參閱 [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)。 請記住，啟用這些功能可能會導致額外的成本。

儲存體帳戶最多可作為兩個目的地帳戶的來源帳戶。 來源和目的地帳戶可能位於相同區域或不同區域。 它們也可以位於不同的訂用帳戶中，以及不同的 Azure Active Directory (Azure AD) 租使用者中。 每一對帳戶只能建立一個複寫原則。

當您設定物件複寫時，會透過 Azure 儲存體資源提供者，在目的地帳戶上建立複寫原則。 建立複寫原則之後，Azure 儲存體指派原則識別碼給它。 然後，您必須使用原則識別碼，將該複寫原則與來源帳戶產生關聯。 來源和目的地帳戶的原則識別碼必須相同才能進行複寫。

若要設定儲存體帳戶的物件複寫原則，您必須將 Azure Resource Manager **參與者** 角色指派給儲存體帳戶層級或更高的層級。 如需詳細資訊，請參閱 azure 角色型存取控制 (Azure RBAC) 檔中的 [azure 內建角色](../../role-based-access-control/built-in-roles.md) 。

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>當您可以存取這兩個儲存體帳戶時，請設定物件複寫

如果您可以存取來源和目的地儲存體帳戶，則可以在這兩個帳戶上設定物件複寫原則。

於 Azure 入口網站中設定物件複寫之前，請先在儲存體帳戶中各自建立來源和目的地容器 (如果尚未建立)。 此外，請啟用來源帳戶的 blob 版本設定和變更摘要，並在目的地帳戶上啟用 blob 版本設定。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

當您針對目的地帳戶設定原則之後，Azure 入口網站會自動在來源帳戶上建立原則。

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

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

1. 根據預設，複製範圍會設定為只複製新的物件。 若要複製容器中的所有物件，或從自訂日期和時間開始複製物件，請選取 [變更] 連結，並設定容器配對的複製範圍。

    下圖顯示自訂複製範圍，它會從指定的日期和時間開始複製物件。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

1. 選取 [儲存並套用] 以建立複寫原則，並開始複寫資料。

設定物件複寫之後，Azure 入口網站會顯示複寫原則和規則，如下圖所示。

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 建立複寫原則，請先安裝 [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) 版或更新版本的 Az PowerShell 模組。 如需如何安裝 Azure PowerShell 的詳細資訊，請參閱[使用 PowerShellGet 安裝 Azure PowerShell](/powershell/azure/install-az-ps)。

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

若要建立具有 Azure CLI 的複寫原則，請先安裝 Azure CLI 2.11.1 版或更新版本。 如需詳細資訊，請參閱 [開始使用 Azure CLI](/cli/azure/get-started-with-azure-cli)。

接下來，在來源和目的地儲存體帳戶上啟用 blob 版本設定，並藉由呼叫 [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) 命令來啟用來源帳戶的變更摘要。 請記得以自有值來取代角括弧中的值：

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

在各自的儲存體帳戶中建立來源和目的地容器。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

藉由呼叫 [az storage account 或-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create)，在目的地帳戶上建立新的複寫原則和相關聯的規則。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

Azure 儲存體在建立新原則時，設定原則識別碼。 若要將其他規則新增至原則，請呼叫 [az storage account 或-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) 並提供原則識別碼。

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

接下來，使用原則識別碼在來源帳戶上建立原則。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>當您只能存取目的地帳戶時，請設定物件複寫

如果您沒有來源儲存體帳戶的許可權，您可以在目的地帳戶上設定物件複寫，並將包含原則定義的 JSON 檔案提供給其他使用者，以在來源帳戶上建立相同的原則。 例如，如果來源帳戶與目的地帳戶位於不同的 Azure AD 租使用者，則您可以使用此方法來設定物件複寫。

請記住，您必須將 Azure Resource Manager **參與者** 角色指派給目的地儲存體帳戶層級或更高的層級，才能建立原則。 如需詳細資訊，請參閱 azure 角色型存取控制 (Azure RBAC) 檔中的 [azure 內建角色](../../role-based-access-control/built-in-roles.md) 。

下表摘要說明每個案例中，JSON 檔案中的原則識別碼和規則識別碼所使用的值。

| 當您為此帳戶建立 JSON 檔案時 .。。 | 將原則識別碼和規則識別碼設定為此值 .。。 |
|-|-|
| 目的地帳戶 | 字串值 *預設*值。 Azure 儲存體會為您建立原則識別碼和規則識別碼。 |
| 來源帳戶 | 當您將目的地帳戶上定義的原則下載為 JSON 檔案時，所傳回的原則識別碼和規則識別碼值。 |

下列範例會使用符合前置詞 *b* 的單一規則來定義目的地帳戶的複寫原則，並設定要複寫之 blob 的最小建立時間。 請記得以自有值來取代角括弧中的值：

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "default",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要使用 Azure 入口網站中的 JSON 檔案來設定目的地帳戶的物件複寫，請遵循下列步驟：

1. 建立本機 JSON 檔案，以定義目的地帳戶的複寫原則。 將 [ **policyId** ] 欄位設定為 [ **預設值** ]，Azure 儲存體將會定義原則識別碼。

    建立 JSON 檔案來定義複寫原則的簡單方式，是先在 Azure 入口網站的兩個儲存體帳戶之間建立測試複寫原則。 然後，您可以下載複寫規則，並視需要修改 JSON 檔案。

1. 在 Azure 入口網站中，流覽至目的地帳戶的 **物件** 複寫設定。
1. 選取 **[上傳複寫規則**]。
1. 上傳 JSON 檔案。 Azure 入口網站會顯示將建立的原則和規則，如下圖所示。

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

1. 選取 [ **上傳** ] 以在目的地帳戶上建立複寫原則。

然後，您可以下載 JSON 檔案，其中包含可提供給其他使用者以設定來源帳戶的原則定義。 若要下載此 JSON 檔案，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至目的地帳戶的 **物件** 複寫設定。
1. 選取您要下載之原則旁的 [ **更多** ] 按鈕，然後選取 [ **下載規則**]，如下圖所示。

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

1. 將 JSON 檔案儲存至您的本機電腦，以與其他使用者共用，以設定來源帳戶的原則。

下載的 JSON 檔案包含 Azure 儲存體為目的地帳戶上的原則建立的原則識別碼。 您必須使用相同的原則識別碼來設定來源帳戶的物件複寫。

請記住，上傳 JSON 檔案以透過 Azure 入口網站建立目的地帳戶的複寫原則，並不會自動在來源帳戶中建立相同的原則。 在 Azure 儲存體開始複寫物件之前，另一位使用者必須在來源帳戶上建立原則。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要從 PowerShell 下載包含目的地帳戶複寫原則定義的 JSON 檔案，請呼叫 [AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) 命令以傳回原則。 然後將原則轉換為 JSON，並將它儲存為本機檔案，如下列範例所示。 請記得以您自己的值取代角括弧中的值和檔案路徑：

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

若要使用 JSON 檔案以 PowerShell 設定來源帳戶的複寫原則，請取出本機檔案，並從 JSON 轉換成物件。 然後呼叫 AzStorageObjectReplicationPolicy 命令來 [設定](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) 來源帳戶的原則，如下列範例所示。 請記得以您自己的值取代角括弧中的值和檔案路徑：

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要從 Azure CLI 將目的地帳戶的複寫原則定義寫入 JSON 檔案，請呼叫 [az storage account 或-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) 命令並輸出至檔案。

下列範例會將原則定義寫入至名為 *policy.js*的 JSON 檔案。 請記得以您自己的值取代角括弧中的值和檔案路徑：

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

若要使用 JSON 檔案，以 Azure CLI 的來源帳戶設定複寫原則，請呼叫 [az storage account 或-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) 命令，並參考檔案 * 上的policy.js* 。 請記得以您自己的值取代角括弧中的值和檔案路徑：

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>檢查 blob 的複寫狀態

您可以使用 Azure 入口網站、PowerShell 或 Azure CLI 檢查來源帳戶中 blob 的複寫狀態。 在複寫完成或失敗之前，不會填入物件複寫屬性。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站的來源帳戶中檢查 blob 的複寫狀態，請遵循下列步驟：

1. 流覽至 Azure 入口網站中的來源帳戶。
1. 找出包含來源 blob 的容器。
1. 選取 blob 以顯示其屬性。 如果已成功複寫 blob，您會在 [ **物件** 複寫] 區段中看到狀態設定為 [ *完成*]。 此外，也會列出負責管理此容器之物件複寫的規則的複寫原則識別碼和識別碼。

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="顯示 Azure 入口網站中複寫規則的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 檢查來源帳戶中 blob 的複寫狀態，請取得 object replication **ReplicationStatus** 屬性的值，如下列範例所示。 請記得以自有值來取代角括弧中的值：

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 檢查來源帳戶中 blob 的複寫狀態，請取得 [物件複寫 **狀態** ] 屬性的值，如下列範例所示：

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
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
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>後續步驟

- [物件複製總覽](object-replication-overview.md)
- [啟用和管理 Blob 版本設定](versioning-enable.md)
- [在 Azure Blob 儲存體中處理變更摘要](storage-blob-change-feed-how-to.md)
