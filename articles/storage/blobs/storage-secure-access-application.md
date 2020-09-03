---
title: 應用程式資料的安全存取
titleSuffix: Azure Storage
description: 使用 SAS 權杖、加密和 HTTPS 保護雲端中的應用程式資料。
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e7dca9224fd213178aa9cee3a284f1eba39fb4fc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001168"
---
# <a name="secure-access-to-application-data"></a>應用程式資料的安全存取

本教學課程是一個系列課程的第三部分。 您了解如何安全存取儲存體帳戶。 

在系列的第三部分中，您將了解如何：

> [!div class="checklist"]
> * 使用 SAS 權杖存取縮圖影像
> * 開啟伺服器端加密
> * 只啟用 HTTPS 進行傳輸

[Azure Blob 儲存體](../common/storage-introduction.md#blob-storage)提供強固的服務來儲存應用程式的檔案。 本教學課程延伸[上一個主題][previous-tutorial]，說明如何從 Web 應用程式安全存取您的儲存體帳戶。 當您完成時，影像會經過加密，而 Web 應用程式會使用安全的 SAS 權杖來存取縮圖影像。

## <a name="prerequisites"></a>必要條件

若要進行本教學課程，您必須已完成先前的儲存體教學課程︰[使用事件方格自動調整已上傳映像的大小][previous-tutorial]。

## <a name="set-container-public-access"></a>設定容器的公用存取

在教學課程系列的這個部分中，使用 SAS 權杖來存取縮圖。 在此步驟中，您會將 *thumbnails* 容器的公用存取設為 `off`。

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>設定縮圖的 SAS 權杖

在此教學課程系列的第一部分中，Web 應用程式會顯示公用容器中的影像。 在系列的這個部分中，您會使用共用存取簽章 (SAS) 權杖來擷取縮圖影像。 SAS 權杖可讓您根據 IP、通訊協定、時間間隔或允許的權限，來限制存取容器或 Blob。 如需關於 SAS 的詳細資訊，請參閱[使用共用存取簽章 (SAS) 對 Azure 儲存體資源授與有限存取權](../common/storage-sas-overview.md)。

在此範例中，原始程式碼存放庫使用具有已更新程式碼範例的 `sasTokens` 分支。 使用 [az webapp deployment source delete](/cli/azure/webapp/deployment/source) 刪除現有的 GitHub 部署。 接著，使用 [az webapp deployment source config](/cli/azure/webapp/deployment/source) 命令設定 Web 應用程式的 GitHub 部署。

在下列命令中，`<web-app>` 是您 Web 應用程式的名稱。

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

存放庫的 `sasTokens` 分支會更新 `StorageHelper.cs` 檔案。 它會將 `GetThumbNailUrls` 工作取代為下列程式碼範例。 更新的工作會使用 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) 來指定 SAS 權杖的開始時間、到期時間和權限，以擷取縮圖 URL。 部署後，Web 應用程式現在會使用 SAS 權杖擷取具有 URL 的縮圖。 下列範例顯示更新的工作：

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

以下是之前的工作所使用的類別、屬性和方法：

| 類別 | 屬性 | 方法 |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [名稱](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [查詢](/dotnet/api/system.uribuilder.query) |  |
|[清單](/dotnet/api/system.collections.generic.list-1) | | [加入](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure 儲存體加密

[Azure 儲存體加密](../common/storage-service-encryption.md)可藉由加密待用資料及處理加密和解密，協助您防護和保護您的資料。 所有資料都使用 256 位元 [AES 加密](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) (可用的最強區塊加密方式之一) 進行加密。

您可以選擇讓 Microsoft 管理加密金鑰，也可以透過 Azure Key Vault 自備金鑰與客戶管理的金鑰。 如需詳細資訊，請參閱[使用客戶管理的金鑰搭配 Azure Key Vault 來管理 Azure 儲存體加密](../common/encryption-customer-managed-keys.md)。

Azure 儲存體加密會自動將所有效能層 (標準和進階)、所有部署模型 (Azure Resource Manager 和傳統) 以及所有「Azure 儲存體」服務 (Blob、佇列、資料表和檔案) 中的資料加密。

## <a name="enable-https-only"></a>只啟用 HTTPS

為了確保在儲存體帳戶之間傳輸資料的要求安全，您可以僅限 HTTPS 要求。 使用 [az storage account update](/cli/azure/storage/account) 命令更新儲存體帳戶所需的通訊協定。

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

利用 `curl` 測試使用 `HTTP` 通訊協定的連線。

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

要求安全傳輸之後，您會收到下列訊息：

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>後續步驟

在系列的第三部分中，您已了解如何安全存取儲存體帳戶，像是如何：

> [!div class="checklist"]
> * 使用 SAS 權杖存取縮圖影像
> * 開啟伺服器端加密
> * 只啟用 HTTPS 進行傳輸

請繼續進行系列的第四部分，以了解如何監視雲端儲存體應用程式並對其進行疑難排解。

> [!div class="nextstepaction"]
> [監視雲端儲存體應用程式並對其進行疑難排解](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
