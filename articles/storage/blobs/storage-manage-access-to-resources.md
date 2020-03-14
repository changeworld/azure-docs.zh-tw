---
title: 管理容器和 blob 的公用讀取權限
titleSuffix: Azure Storage
description: 了解如何讓容器與 Blob 可供匿名存取，以及如何以程式設計方式存取。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 4d9a54c220861b19d67b07998e609ee72897446a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255479"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>管理對容器與 Blob 的匿名讀取權限。

您可以對 Azure Blob 儲存體中的容器及其 Blob 啟用匿名與公用讀取權限。 如此您就可以將這些資源的唯讀存取權限授與他人，而無須共用您的帳戶金鑰，也無須要求共用存取簽章 (SAS)。

公用讀取權限適用於您想要某些 Blob 永遠可供匿名讀取存取的狀況。 對於更深入的控管需求，您可以建立共用存取簽章。 共用存取簽章可讓您針對一段特定時間提供不同權限的限制存取。 如需建立共用存取簽章的詳細資訊，請參閱[在 Azure 儲存體中使用共用存取簽章 (SAS)](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>授與容器和 Blob 的匿名使用者權限

根據預設，容器和其中的任何 blob 只能由已獲得適當許可權的使用者進行存取。 若要為匿名使用者授與容器及其 blob 的讀取權限，您可以設定容器公用存取層級。 當您授與容器的公用存取權時，匿名使用者可以讀取可公開存取之容器內的 blob，而不需要授權要求。

您可以為容器設定下列權限︰

- **無公用讀取權限︰** 只有儲存體帳戶擁有者可以存取容器和其 Blob。 這是所有新建容器的預設值。
- **僅對 Blob 有公用讀取權限：** 您可以透過匿名要求讀取容器內的 Blob，但您無法使用容器資料。 匿名用戶端無法列舉容器內的 Blob。
- **容器和其 blob 的公用讀取權限：** 匿名要求可以讀取所有容器和 blob 資料。 用戶端可以透過匿名要求列舉容器內的 Blob，但無法列舉儲存體帳戶內的容器。

### <a name="set-container-public-access-level-in-the-azure-portal"></a>在 Azure 入口網站中設定容器公用存取層級

從[Azure 入口網站](https://portal.azure.com)中，您可以更新一或多個容器的公用存取層級：

1. 流覽至您在 Azure 入口網站中的儲存體帳戶總覽。
1. 在功能表分頁的 [ **Blob 服務**] 底下，選取 [ **blob**]。
1. 選取您要設定其公用存取層級的容器。
1. 使用 [**變更存取層級**] 按鈕來顯示公用存取設定。
1. 從 [**公用存取層級**] 下拉式清單中選取想要的公用存取層級，然後按一下 [確定] 按鈕，將變更套用至選取的容器。

下列螢幕擷取畫面顯示如何變更所選容器的公用存取層級。

![顯示如何在入口網站中設定公用存取層級的螢幕擷取畫面](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> 您無法變更個別 blob 的公用存取層級。 公用存取層級只會在容器層級設定。

### <a name="set-container-public-access-level-with-net"></a>使用 .NET 設定容器公用存取層級

若要使用適用于 .NET 的 Azure 儲存體用戶端程式庫來設定容器的許可權，請先呼叫下列其中一個方法來取得容器的現有許可權：

- [GetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissions)
- [GetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.getpermissionsasync)

接下來，在**GetPermissions**方法傳回的[BlobContainerPermissions](/dotnet/api/microsoft.azure.storage.blob.blobcontainerpermissions)物件上設定**PublicAccess**屬性。

最後，呼叫下列其中一種方法來更新容器的許可權：

- [SetPermissions](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissions)
- [SetPermissionsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setpermissionsasync)

下列範例將容器的權限設為完整公用讀取權限。 若只要將 Blob 的權限設為公用讀取權限，請將 **PublicAccess** 屬性設為 **BlobContainerPublicAccessType.Blob**。 若要移除匿名使用者的所有權限，請將屬性設為 **BlobContainerPublicAccessType.Off**。

```csharp
private static async Task SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = await container.GetPermissionsAsync();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    await container.SetPermissionsAsync(permissions);

    Console.WriteLine("Container {0} - permissions set to {1}", container.Name, permissions.PublicAccess);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>匿名存取容器與 Blob

匿名存取容器與 Blob 的用戶端可使用不需要認證的建構函式。 下列範例示範以匿名方式參考容器和 blob 的幾種不同方法。

### <a name="create-an-anonymous-client-object"></a>建立匿名用戶端物件

您可以藉由提供帳戶的 Blob 儲存體端點，為匿名存取建立新的服務用戶端物件。 不同，您也必須知道可供匿名存取的帳戶中的容器名稱。

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint for your account.
    CloudBlobClient blobClient = new CloudBlobClient(
        new Uri(@"https://storagesamples.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. 
    // Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>匿名參考容器

如果您有可供匿名使用之容器的 URL，您可以使用該 URL 直接參考容器。

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    // Note this is only possible when the container supports full public read access.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>匿名參考 Blob

如果您有可供匿名存取之 Blob 的 URL，您可以使用該 URL 直接參考 Blob：

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

## <a name="next-steps"></a>後續步驟

- [授權 Azure 儲存體的存取權](../common/storage-auth.md)
- [使用共用存取簽章（SAS）授與 Azure 儲存體資源的有限存取權](../common/storage-sas-overview.md)
- [Blob 服務 REST API](/rest/api/storageservices/blob-service-rest-api)
