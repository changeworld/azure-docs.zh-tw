---
title: 使用 .NET 以匿名方式存取公用容器和 blob
titleSuffix: Azure Storage
description: 使用適用于 .NET 的 Azure 儲存體用戶端程式庫，以匿名方式存取公用容器和 blob。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/02/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 2437c5b3272163b3931d7417c84e761c591aec85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90088811"
---
# <a name="access-public-containers-and-blobs-anonymously-with-net"></a>使用 .NET 以匿名方式存取公用容器和 blob

Azure 儲存體支援容器和 blob 的選擇性公用讀取權限。 用戶端可以使用 Azure 儲存體用戶端程式庫，以及使用支援資料存取 Azure 儲存體的其他工具和公用程式，以匿名方式存取公用容器和 blob。

本文說明如何從 .NET 存取公用容器或 blob。 如需在容器上設定匿名讀取權限的相關資訊，請參閱 [設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)。 如需防止所有匿名存取儲存體帳戶的相關資訊，請參閱 [防止對容器和 blob 進行匿名公用讀取存取](anonymous-read-access-prevent.md)。

匿名存取容器與 Blob 的用戶端可使用不需要認證的建構函式。 下列範例顯示以匿名方式參考容器和 blob 的幾種不同方式。

## <a name="create-an-anonymous-client-object"></a>建立匿名用戶端物件

您可以藉由提供帳戶的 Blob 儲存體端點來建立匿名存取的新服務用戶端物件。 不同，您也必須知道可供匿名存取的帳戶中的容器名稱。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_CreateAnonymousBlobClient":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

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

---

## <a name="reference-a-container-anonymously"></a>匿名參考容器

如果您有可供匿名使用之容器的 URL，您可以使用該 URL 直接參考容器。

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_ListBlobsAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

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

---

## <a name="reference-a-blob-anonymously"></a>匿名參考 Blob

如果您有可供匿名存取之 Blob 的 URL，您可以使用該 URL 直接參考 Blob：

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Security.cs" id="Snippet_DownloadBlobAnonymously":::

# <a name="net-v11-sdk"></a>[\.NET v11 SDK](#tab/dotnet11)

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(
        new Uri(@"https://storagesamples.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", FileMode.Create);
}
```

---

## <a name="next-steps"></a>後續步驟

- [設定容器和 blob 的匿名公用讀取權限](anonymous-read-access-configure.md)
- [防止對容器和 blob 進行匿名公用讀取存取](anonymous-read-access-prevent.md)
- [授權存取 Azure 儲存體](../common/storage-auth.md)
