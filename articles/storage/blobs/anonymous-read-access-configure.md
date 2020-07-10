---
title: 設定容器和 blob 的匿名公用讀取權限
titleSuffix: Azure Storage
description: 瞭解如何針對儲存體帳戶啟用或停用對 blob 資料的匿名存取。 設定容器公用存取設定，讓容器和 blob 可供匿名存取。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2020
ms.author: tamram
ms.reviewer: fryu
ms.openlocfilehash: af589874021baaf04a423b7bbaa0e36528eda93c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209415"
---
# <a name="configure-anonymous-public-read-access-for-containers-and-blobs"></a>設定容器和 blob 的匿名公用讀取權限

Azure 儲存體支援對容器和 blob 進行匿名的公用讀取權限。 根據預設，對容器及其 blob 的所有要求都必須使用 Azure Active Directory (Azure AD) 或共用金鑰授權來獲得授權。 當您將容器的公用存取層級設定設為允許匿名存取時，用戶端可以讀取該容器中的資料，而不需要授權要求。

> [!WARNING]
> 設定容器的公用存取權時，任何用戶端都可以讀取該容器中的資料。 公用存取會帶來潛在的安全性風險，因此，如果您的案例不需要它，Microsoft 建議您將它停用於儲存體帳戶。 如需詳細資訊，請參閱[防止對容器和 blob 的匿名公用讀取權限](anonymous-read-access-prevent.md)。

若要設定容器的公用存取，您必須執行兩個步驟：

1. 啟用儲存體帳戶的公用存取
1. 設定容器的公用存取設定

本文說明如何設定容器和其 blob 的匿名公用讀取權限。 如需如何以匿名方式從用戶端應用程式存取 blob 資料的詳細資訊，請參閱[以匿名方式使用 .Net 存取公用容器和 blob](anonymous-read-access-client.md)。

## <a name="enable-or-disable-public-read-access-for-a-storage-account"></a>啟用或停用儲存體帳戶的公用讀取權限

根據預設，會針對儲存體帳戶啟用公用存取。 停用公用存取可防止所有匿名存取該帳戶中的容器和 blob。 為了提升安全性，Microsoft 建議您停用儲存體帳戶的公用存取權，除非您的案例需要使用者以匿名方式存取 blob 資源。

> [!WARNING]
> 停用儲存體帳戶的公用存取權，會覆寫該儲存體帳戶中所有容器的公用存取設定。 停用儲存體帳戶的公用存取時，任何未來對該帳戶的匿名要求都會失敗。

若要啟用或停用儲存體帳戶的公用存取，請使用 Azure 入口網站或 Azure CLI 設定帳戶的**blobPublicAccess**屬性。 此屬性適用于使用 Azure Resource Manager 部署模型建立的所有儲存體帳戶。 如需詳細資訊，請參閱[儲存體帳戶總覽](../common/storage-account-overview.md)。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要在 Azure 入口網站中啟用或停用儲存體帳戶的公用存取，請遵循下列步驟：

1. 在 Azure 入口網站中巡覽至您的儲存體帳戶。
1. **在 [** **設定**] 下找出設定。
1. 將 [ **Blob 公用存取**] 設定為 [**停用**] 或 [**啟用**]。

    :::image type="content" source="media/anonymous-read-access-configure/blob-public-access-portal.png" alt-text="顯示如何啟用或停用帳戶之 blob 公用存取的螢幕擷取畫面":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要啟用或停用具有 Azure CLI 之儲存體帳戶的公用存取，請先呼叫[az resource show](/cli/azure/resource#az-resource-show)命令，以取得儲存體帳戶的資源識別碼。 接下來，呼叫[az resource update](/cli/azure/resource#az-resource-update)命令來設定儲存體帳戶的**allowBlobPublicAccess**屬性。 若要啟用公用存取，請將**allowBlobPublicAccess**屬性設定為 true;若要停用，請將它設定為**false**。

下列範例會停用儲存體帳戶的公用 blob 存取。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
storage_account_id=$(az resource show \
    --name anonpublicaccess \
    --resource-group storagesamples-rg \
    --resource-type Microsoft.Storage/storageAccounts \
    --query id \
    --output tsv)

az resource update \
    --ids $storage_account_id \
    --set properties.allowBlobPublicAccess=false
    ```
```

若要檢查是否已啟用 Azure CLI 的公用存取，請呼叫[az resource show](/cli/azure/resource#az-resource-show)命令，並查詢**allowBlobPublicAccess**屬性：

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

---

> [!NOTE]
> 停用儲存體帳戶的公用存取，並不會影響任何裝載于該儲存體帳戶中的靜態網站。 **$Web**容器一律可公開存取。

## <a name="check-the-public-access-setting-for-a-storage-account"></a>檢查儲存體帳戶的公用存取設定

若要檢查儲存體帳戶的公用存取設定，請取得**allowBlobPublicAccess**屬性的值。 若要一次檢查一個大型儲存體帳戶的這個屬性，請使用 [Azure Resource Graph Explorer]。

預設不會設定**allowBlobPublicAccess**屬性，而且在您明確設定之前不會傳回值。 當屬性值為 null 時，儲存體帳戶預設為允許公用存取。

### <a name="check-the-public-access-setting-for-a-single-storage-account"></a>檢查單一儲存體帳戶的公用存取設定

若要使用 Azure CLI 來檢查單一儲存體帳戶的公用存取設定，請呼叫[az resource show](/cli/azure/resource#az-resource-show)命令，並查詢**allowBlobPublicAccess**屬性：

```azurecli-interactive
az resource show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --resource-type Microsoft.Storage/storageAccounts \
    --query properties.allowBlobPublicAccess \
    --output tsv
```

### <a name="check-the-public-access-setting-for-a-set-of-storage-accounts"></a>檢查一組儲存體帳戶的公用存取設定

若要在具有最佳效能的一組儲存體帳戶上檢查公用存取設定，您可以使用 Azure 入口網站中的 [Azure Resource Graph Explorer]。 若要深入瞭解如何使用 Resource Graph Explorer，請參閱[快速入門：使用 Azure Resource Graph Explorer 執行您的第一個 Resource Graph 查詢](/azure/governance/resource-graph/first-query-portal)。

在 Resource Graph Explorer 中執行下列查詢，會傳回儲存體帳戶的清單，並顯示每個帳戶的**allowBlobPublicAccess**屬性值：

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowBlobPublicAccess = parse_json(properties).allowBlobPublicAccess
| project subscriptionId, resourceGroup, name, allowBlobPublicAccess
| order by subscriptionId, resourceGroup, name asc
```

## <a name="set-the-public-access-level-for-a-container"></a>設定容器的公用存取層級

若要為匿名使用者授與容器及其 blob 的讀取權限，請先啟用儲存體帳戶的公用存取，然後設定容器的公用存取層級。 如果已停用儲存體帳戶的公用存取，您將無法設定容器的公用存取權。

啟用儲存體帳戶的公用存取時，您可以使用下列許可權來設定容器：

- **沒有公用讀取權限：** 容器和其 blob 只能透過授權的要求來存取。 此選項是所有新容器的預設值。
- **僅適用于 blob 的公用讀取權限：** 匿名要求可以讀取容器內的 blob，但容器資料無法以匿名方式使用。 匿名用戶端無法列舉容器內的 Blob。
- **容器和其 blob 的公用讀取權限：** 除了容器許可權設定和容器中繼資料以外，匿名要求可以讀取容器和 blob 資料。 用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

您無法變更個別 blob 的公用存取層級。 公用存取層級只會在容器層級設定。

若要設定容器的公用存取層級，請使用 Azure 入口網站或 Azure CLI。 您可以在建立容器時設定容器的公用存取層級，或在現有的容器上更新此設定。

# <a name="azure-portal"></a>[Azure 入口網站](#tab/portal)

若要更新 Azure 入口網站中一個或多個現有容器的公用存取層級，請遵循下列步驟：

1. 流覽至您在 Azure 入口網站中的儲存體帳戶總覽。
1. 在功能表分頁的 [ **Blob 服務**] 底下，選取 [**容器**]。
1. 選取您要設定其公用存取層級的容器。
1. 使用 [**變更存取層級**] 按鈕來顯示公用存取設定。
1. 從 [**公用存取層級**] 下拉式清單中選取想要的公用存取層級，然後按一下 [確定] 按鈕，將變更套用至選取的容器。

    ![螢幕擷取畫面顯示如何在入口網站中設定公用存取層級](./media/anonymous-read-access-configure/configure-public-access-container.png)

停用儲存體帳戶的公用存取時，無法設定容器的公用存取層級。 如果您嘗試設定容器的公用存取層級，您會看到設定已停用，因為帳戶已禁止公用存取。

:::image type="content" source="media/anonymous-read-access-configure/container-public-access-blocked.png" alt-text="螢幕擷取畫面，顯示停用公用存取時，會封鎖設定容器公用存取層級":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 來更新一或多個容器的公用存取層級，請呼叫[az storage container set 許可權](/cli/azure/storage/container#az-storage-container-set-permission)命令。 藉由將您的帳戶金鑰、連接字串或共用存取簽章傳入 (SAS) 來授權這項操作。 設定容器的公用存取層級的[Set CONTAINER ACL](/rest/api/storageservices/set-container-acl)作業不支援使用 Azure AD 進行授權。 如需詳細資訊，請參閱[呼叫 blob 和佇列資料作業的許可權](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下列範例會設定容器的公用存取設定，以啟用容器和其 blob 的匿名存取。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage container set-permission \
    --name <container-name> \
    --account-name <account-name> \
    --public-access container \
    --account-key <account-key> \
    --auth-mode key
```

停用儲存體帳戶的公用存取時，無法設定容器的公用存取層級。 如果您嘗試設定容器的公用存取層級，就會發生錯誤，指出儲存體帳戶上不允許公用存取。

---

## <a name="check-the-container-public-access-setting"></a>檢查容器的公用存取設定

若要檢查一或多個容器的公用存取設定，您可以使用 Azure 入口網站、PowerShell、Azure CLI、其中一個 Azure 儲存體用戶端程式庫，或 Azure 儲存體資源提供者。 下列各節提供一些範例。  

### <a name="check-the-public-access-setting-for-a-single-container"></a>檢查單一容器的公用存取設定

若要使用 Azure CLI 取得一或多個容器的公用存取層級，請呼叫[az storage container show 許可權](/cli/azure/storage/container#az-storage-container-show-permission)命令。 藉由將您的帳戶金鑰、連接字串或共用存取簽章傳入 (SAS) 來授權這項操作。 傳回容器的公用存取層級的「[取得容器 ACL](/rest/api/storageservices/get-container-acl) 」作業不支援使用 Azure AD 進行授權。 如需詳細資訊，請參閱[呼叫 blob 和佇列資料作業的許可權](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。

下列範例會讀取容器的公用存取設定。 請記得以您自己的值取代括弧中的預留位置值：

```azurecli-interactive
az storage container show-permission \
    --name <container-name> \
    --account-name <account-name> \
    --account-key <account-key>
    --auth-mode key
```

### <a name="check-the-public-access-setting-for-a-set-of-containers"></a>檢查一組容器的公用存取設定

藉由列出容器並檢查公用存取設定，可以檢查一或多個儲存體帳戶中的哪些容器已設定為公開存取。 當儲存體帳戶不包含大量的容器，或當您在少數儲存體帳戶之間檢查設定時，此方法是一個實用的選項。 不過，如果您嘗試列舉大量的容器，效能可能會受到影響。

下列範例會使用 PowerShell 來取得儲存體帳戶中所有容器的公用存取設定。 請記得以您自己的值取代括弧中的預留位置值：

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

Get-AzStorageContainer -Context $ctx | Select Name, PublicAccess
```

## <a name="next-steps"></a>後續步驟

- [防止對容器和 blob 的匿名公用讀取權限](anonymous-read-access-prevent.md)
- [以匿名方式使用 .NET 存取公用容器和 blob](anonymous-read-access-client.md)
- [授權存取 Azure 儲存體](../common/storage-auth.md)