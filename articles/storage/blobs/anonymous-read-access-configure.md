---
title: 設定容器和 blob 的匿名公用讀取權限
titleSuffix: Azure Storage
description: 瞭解如何允許或禁止匿名存取儲存體帳戶的 blob 資料。 設定容器公用存取設定，讓容器和 blob 可供匿名存取。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/03/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: feac7b890c973b1541c5362f860432687082953f
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533871"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>設定容器和 blob 的匿名公用讀取權限

Azure 儲存體支援容器和 blob 的選擇性匿名公用讀取權限。 根據預設，永遠不允許對您的資料進行匿名存取。 除非您明確啟用匿名存取，否則對容器及其 blob 的所有要求都必須獲得授權。 當您將容器的公用存取層級設定為允許匿名存取時，用戶端可以讀取該容器中的資料，而不會授權要求。

> [!WARNING]
> 當容器設定為公用存取時，任何用戶端都可以讀取該容器中的資料。 公開存取會帶來潛在的安全性風險，因此，如果您的案例不需要它，Microsoft 建議您將它不允許用於儲存體帳戶。 如需詳細資訊，請參閱 [防止對容器和 blob 進行匿名公用讀取存取](anonymous-read-access-prevent.md)。

本文說明如何設定容器及其 blob 的匿名公用讀取權限。 如需有關如何從用戶端應用程式匿名存取 blob 資料的詳細資訊，請參閱 [使用 .net 匿名存取公用容器和 blob](anonymous-read-access-client.md)。

## <a name="about-anonymous-public-read-access"></a>關於匿名公用讀取權限

預設一律禁止公用存取您的資料。 有兩個不同的設定會影響公用存取：

1. **允許對儲存體帳戶進行公用存取。** 根據預設，儲存體帳戶允許具有適當許可權的使用者啟用容器的公用存取。 除非使用者採取額外的步驟來明確設定容器的公用存取設定，否則 Blob 資料無法供公用存取。
1. **設定容器的公用存取設定。** 預設會停用容器的公用存取設定，這表示每個對容器或其資料的要求都需要授權。 具有適當許可權的使用者可以修改容器的公用存取設定，只在允許儲存體帳戶進行匿名存取時，才啟用匿名存取。

下表摘要說明這兩項設定如何共同影響容器的公用存取。

| 公用存取設定 | 容器 (預設設定已停用公用存取)  | 容器的公用存取設定為容器 | 將容器設定為 Blob 的公用存取 |
|--|--|--|--|
| 儲存體帳戶不允許公用存取 | 沒有儲存體帳戶中任何容器的公用存取權。 | 沒有儲存體帳戶中任何容器的公用存取權。 儲存體帳戶設定會覆寫容器設定。 | 沒有儲存體帳戶中任何容器的公用存取權。 儲存體帳戶設定會覆寫容器設定。 |
| 儲存體帳戶 (預設設定允許公用存取)  | 沒有此容器的公用存取 (預設設定) 。 | 此容器及其 blob 允許公用存取。 | 允許公用存取此容器中的 blob，但無法存取容器本身。 |

## <a name="allow-or-disallow-public-read-access-for-a-storage-account"></a>允許或不允許儲存體帳戶的公用讀取權限

根據預設，儲存體帳戶會設定為允許具有適當許可權的使用者啟用容器的公用存取。 允許公開存取時，具有適當許可權的使用者可以修改容器的公用存取設定，以啟用該容器中資料的匿名公用存取權。 除非使用者採取額外的步驟來明確設定容器的公用存取設定，否則 Blob 資料永遠不會供公開存取。

請記住，容器的公用存取一律預設為關閉，且必須明確設定為允許匿名要求。 無論儲存體帳戶上的設定為何，除非具有適當許可權的使用者採用此額外步驟來啟用容器的公用存取，否則您的資料永遠不會提供公開存取。

不允許儲存體帳戶的公用存取會防止對該帳戶中的所有容器和 blob 進行匿名存取。 當帳戶不允許公用存取時，無法設定容器的公用存取設定來允許匿名存取。 為了提高安全性，除非您的案例要求使用者匿名存取 blob 資源，否則 Microsoft 建議您不要對您的儲存體帳戶允許公用存取。

> [!IMPORTANT]
> 不允許儲存體帳戶的公用存取會覆寫該儲存體帳戶中所有容器的公用存取設定。 當儲存體帳戶不允許公用存取時，對該帳戶的任何未來匿名要求都將會失敗。 變更此設定之前，請務必瞭解對可能會匿名存取儲存體帳戶中資料的用戶端應用程式所造成的影響。 如需詳細資訊，請參閱 [防止對容器和 blob 進行匿名公用讀取存取](anonymous-read-access-prevent.md)。

若要允許或不允許存取儲存體帳戶的公用存取權，請設定帳戶的 **AllowBlobPublicAccess** 屬性。 此屬性適用于使用 Azure Resource Manager 部署模型建立的所有儲存體帳戶。 如需詳細資訊，請參閱 [儲存體帳戶總覽](../common/storage-account-overview.md)。

預設不會針對儲存體帳戶設定 **AllowBlobPublicAccess** 屬性，而且在您明確設定它之前，不會傳回值。 當屬性值為 **null** 或 **true** 時，儲存體帳戶允許公用存取。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要允許或不允許 Azure 入口網站中儲存體帳戶的公用存取，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. 在 [**設定**] 下找 **出設定。**
1. 將 **Blob 公用存取** 設定為 [ **已啟用** ] 或 [ **已停用**]。

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="顯示如何允許或不允許帳戶的 blob 公用存取的螢幕擷取畫面":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要允許或不允許使用 PowerShell 的儲存體帳戶進行公用存取，請安裝 [Azure PowerShell 4.4.0 版](https://www.powershellgallery.com/packages/Az/4.4.0) 或更新版本。 接下來，為新的或現有的儲存體帳戶設定 **AllowBlobPublicAccess** 屬性。

下列範例會建立儲存體帳戶，並明確地將 **AllowBlobPublicAccess** 屬性設定為 **true**。 然後，它會更新儲存體帳戶，以將 **AllowBlobPublicAccess** 屬性設定為 **false**。 此範例也會在每個案例中捕獲屬性值。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account with AllowBlobPublicAccess set to true (or null).
New-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -Location $location `
    -SkuName Standard_GRS
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property for the newly created storage account.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess

# Set AllowBlobPublicAccess set to false
Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowBlobPublicAccess $false

# Read the AllowBlobPublicAccess property.
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowBlobPublicAccess
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要允許或不允許具有 Azure CLI 之儲存體帳戶的公用存取，請安裝 Azure CLI 2.9.0 版版或更新版本。 如需詳細資訊，請參閱 [安裝 Azure CLI](/cli/azure/install-azure-cli)。 接下來，為新的或現有的儲存體帳戶設定 **allowBlobPublicAccess** 屬性。

下列範例會建立儲存體帳戶，並明確地將 **allowBlobPublicAccess** 屬性設定為 **true**。 然後，它會更新儲存體帳戶，以將 **allowBlobPublicAccess** 屬性設定為 **false**。 此範例也會在每個案例中捕獲屬性值。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --kind StorageV2 \
    --location <location> \
    --allow-blob-public-access true

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv

az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-blob-public-access false

az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowBlobPublicAccess \
    --output tsv
```

# <a name="template"></a>[範本](#tab/template)

若要允許或不允許具有範本的儲存體帳戶的公用存取，請建立 **AllowBlobPublicAccess** 屬性設定為 **true** 或 **false** 的範本。 下列步驟說明如何在 Azure 入口網站中建立範本。

1. 在 [Azure 入口網站中，選擇 [ **建立資源**]。
1. 在 **[搜尋 Marketplace**] 中，輸入 **範本部署**，然後按 **enter**。
1. 選擇 [**使用自訂範本部署範本部署 (])  (預覽)**，選擇 **[建立]，然後****在編輯器中選擇 [建立您自己的範本**]。
1. 在範本編輯器中，貼上下列 JSON 以建立新的帳戶，並將 **AllowBlobPublicAccess** 屬性設定為 **true** 或 **false**。 請記得將角括弧中的預留位置取代為您自己的值。

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {
            "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'template')]"
        },
        "resources": [
            {
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "location": "<location>",
            "properties": {
                "allowBlobPublicAccess": false
            },
            "dependsOn": [],
            "sku": {
              "name": "Standard_GRS"
            },
            "kind": "StorageV2",
            "tags": {}
            }
        ]
    }
    ```

1. 儲存範本。
1. 指定資源群組參數，然後選擇 [ **審核 + 建立** ] 按鈕來部署範本，並建立已設定 **allowBlobPublicAccess** 屬性的儲存體帳戶。

---

> [!NOTE]
> 若不允許儲存體帳戶的公用存取，就不會影響該儲存體帳戶中裝載的任何靜態網站。 **$Web** 容器一律可公開存取。
>
> 在您更新儲存體帳戶的公用存取設定之後，最多可能需要30秒的時間，變更才會完全傳播。

允許或不允許 blob 公用存取需要 Azure 儲存體資源提供者的2019-04-01 版或更新版本。 如需詳細資訊，請參閱 [Azure 儲存體資源提供者 REST API](/rest/api/storagerp/)。

本節中的範例示範如何讀取儲存體帳戶的 **AllowBlobPublicAccess** 屬性，以判斷目前是否允許或不允許公用存取。 若要深入瞭解如何確認帳戶的公用存取設定是否已設定為防止匿名存取，請參閱 [補救匿名公用存取](anonymous-read-access-prevent.md#remediate-anonymous-public-access)。

## <a name="set-the-public-access-level-for-a-container"></a>設定容器的公用存取層級

若要為匿名使用者授與容器及其 blob 的讀取權限，請先允許儲存體帳戶的公用存取，然後設定容器的公用存取層級。 如果拒絕存取儲存體帳戶的公用存取權，您將無法設定容器的公用存取。

當儲存體帳戶允許公用存取時，您可以使用下列許可權來設定容器：

- **沒有公開讀取權限：** 只能使用已授權的要求來存取容器及其 blob。 此選項是所有新容器的預設值。
- **僅限 blob 的公用讀取權限：** 匿名要求可以讀取容器內的 blob，但無法匿名使用容器資料。 匿名用戶端無法列舉容器內的 Blob。
- **容器及其 blob 的公用讀取權限：** 除了容器許可權設定和容器中繼資料以外，匿名要求可以讀取容器和 blob 資料。 用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

您無法變更個別 blob 的公用存取層級。 公用存取層級只會在容器層級設定。 您可以在建立容器時設定容器的公用存取層級，也可以更新現有容器上的設定。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要更新 Azure 入口網站中一或多個現有容器的公用存取層級，請遵循下列步驟：

1. 在 Azure 入口網站中，流覽至您的儲存體帳戶總覽。
1. 在 [功能表] 分頁的 [ **Blob 服務** ] 底下，選取 [ **容器**]。
1. 選取您要設定其公用存取層級的容器。
1. 使用 [ **變更存取層級** ] 按鈕以顯示公用存取設定。
1. 從 [ **公用存取層級** ] 下拉式清單中選取所需的公用存取層級，然後按一下 [確定] 按鈕，將變更套用至選取的容器。

    ![螢幕擷取畫面顯示如何在入口網站中設定公用存取層級](./media/anonymous-read-access-configure/configure-public-access-container.png)

當儲存體帳戶不允許公用存取時，無法設定容器的公用存取層級。 如果您嘗試設定容器的公用存取層級，您會看到設定已停用，因為帳戶不允許公用存取。

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="螢幕擷取畫面，顯示在不允許公用存取時封鎖設定容器的公用存取層級":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

若要使用 PowerShell 來更新一或多個容器的公用存取層級，請呼叫 [AzStorageContainerAcl](/powershell/module/az.storage/set-azstoragecontaineracl) 命令。 將您的帳戶金鑰、連接字串或共用存取簽章傳入 (SAS) ，以授權這項作業。 設定容器的公用存取層級的 [設定容器 ACL](/rest/api/storageservices/set-container-acl) 作業不支援 Azure AD 的授權。 如需詳細資訊，請參閱 [呼叫 blob 和佇列資料作業的許可權](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下列範例會建立已停用公用存取的容器，然後更新容器的公用存取設定，以允許匿名存取容器及其 blob。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
# Set variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Get context object.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

# Create a new container with public access setting set to Off.
$containerName = "<container>"
New-AzStorageContainer -Name $containerName -Permission Off -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx

# Update the container's public access setting to Container.
Set-AzStorageContainerAcl -Container $containerName -Permission Container -Context $ctx

# Read the container's public access setting.
Get-AzStorageContainerAcl -Container $containerName -Context $ctx
```

當儲存體帳戶不允許公用存取時，無法設定容器的公用存取層級。 如果您嘗試設定容器的公用存取層級，Azure 儲存體會傳回錯誤，指出儲存體帳戶上不允許公用存取。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 更新一或多個容器的公用存取層級，請呼叫 [az storage container set 許可權](/cli/azure/storage/container#az-storage-container-set-permission) 命令。 將您的帳戶金鑰、連接字串或共用存取簽章傳入 (SAS) ，以授權這項作業。 設定容器的公用存取層級的 [設定容器 ACL](/rest/api/storageservices/set-container-acl) 作業不支援 Azure AD 的授權。 如需詳細資訊，請參閱 [呼叫 blob 和佇列資料作業的許可權](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下列範例會建立已停用公用存取的容器，然後更新容器的公用存取設定，以允許匿名存取容器及其 blob。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage container create \
    --name <container-name> \
    --account-name <account-name> \
    --resource-group <resource-group>
    --public-access off \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key

az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key

az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key> \
    --auth-mode key
```

當儲存體帳戶不允許公用存取時，無法設定容器的公用存取層級。 如果您嘗試設定容器的公用存取層級，Azure 儲存體會傳回錯誤，指出儲存體帳戶上不允許公用存取。

# <a name="template"></a>[範本](#tab/template)

N/A。

---

## <a name="check-the-public-access-setting-for-a-set-of-containers"></a>檢查一組容器的公用存取設定

您可以藉由列出容器並檢查公用存取設定，檢查一或多個儲存體帳戶中的哪些容器已設定為公開存取。 當儲存體帳戶未包含大量容器，或當您在少量的儲存體帳戶中檢查設定時，此方法是個實用的選項。 但是，如果您嘗試列舉大量的容器，效能可能會受到影響。

下列範例會使用 PowerShell 來取得儲存體帳戶中所有容器的公用存取設定。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>後續步驟

- [防止對容器和 blob 進行匿名公用讀取存取](anonymous-read-access-prevent.md)
- [使用 .NET 以匿名方式存取公用容器和 blob](anonymous-read-access-client.md)
- [授權存取 Azure 儲存體](../common/storage-auth.md)
