---
title: 使用 .NET - Azure 存儲創建或刪除 Blob 容器
description: 瞭解如何使用 .NET 用戶端庫在 Azure 存儲帳戶中創建或刪除 Blob 容器。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c95ed6dde3c00c0688ccfd58565fd112427c8899
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135933"
---
# <a name="create-or-delete-a-container-in-azure-storage-with-net"></a>使用 .NET 在 Azure 存儲中創建或刪除容器

Azure 存儲中的 Blob 被組織到容器中。 必須先創建容器，然後才能上載 Blob。 本文演示如何使用 .NET 的 Azure[存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)創建和刪除容器。

## <a name="name-a-container"></a>命名容器

容器名稱必須是有效的 DNS 名稱，因為它構成用於解決容器或其 blob 的唯一 URI 的一部分。 命名容器時遵循以下規則：

- 容器名稱可以長在 3 到 63 個字元之間。
- 容器名稱必須以字母或數位開頭，並且只能包含小寫字母、數位和破折號 （-） 字元。
- 容器名稱中不允許使用兩個或多個連續短劃線字元。

容器的 URI 採用以下格式：

`https://myaccount.blob.core.windows.net/mycontainer`

## <a name="create-a-container"></a>建立容器

要創建容器，請調用以下方法之一：

- [建立](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.create)
- [CreateAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createasync)
- [CreateIfNotExists](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexists)
- [創建不存在的同步](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.createifnotexistsasync)

如果具有相同名稱的容器已存在，**則"創建**和**創建 Async"** 方法將引發異常。

**CreateIfNotNot 存在**和**創建IfNotExistsAsync**方法返回一個布林值，指示容器是否已創建。 如果已存在同名容器，則這些方法將返回**False**以指示未創建新容器。

容器會在儲存體帳戶下方直接建立。 您無法將某個容器巢於另一個容器下方。

以下示例非同步創建容器：

```csharp
private static async Task<CloudBlobContainer> CreateSampleContainerAsync(CloudBlobClient blobClient)
{
    // Name the sample container based on new GUID, to ensure uniqueness.
    // The container name must be lowercase.
    string containerName = "container-" + Guid.NewGuid();

    // Get a reference to a sample container.
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Create the container if it does not already exist.
        bool result = await container.CreateIfNotExistsAsync();
        if (result == true)
        {
            Console.WriteLine("Created container {0}", container.Name);
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }

    return container;
}
```

## <a name="create-the-root-container"></a>創建根容器

根容器會做為儲存體帳戶的預設容器。 每個存儲帳戶可能有一個根容器，必須命名為 *$root。* 必須顯式創建或刪除根容器。

可以引用存儲在根容器中的 Blob，而不包括根容器名稱。 根容器使您能夠在存儲帳戶層次結構的頂層引用 Blob。 例如，您可以依照下述方式參考位於根容器中的 Blob：

`https://myaccount.blob.core.windows.net/default.html`

以下示例同步創建根容器：

```csharp
private static void CreateRootContainer(CloudBlobClient blobClient)
{
    try
    {
        // Create the root container if it does not already exist.
        CloudBlobContainer container = blobClient.GetContainerReference("$root");
        if (container.CreateIfNotExists())
        {
            Console.WriteLine("Created root container.");
        }
        else
        {
            Console.WriteLine("Root container already exists.");
        }
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
    }
}
```

## <a name="delete-a-container"></a>刪除容器

要刪除 .NET 中的容器，請使用以下方法之一：

- [刪除](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)
- [刪除存在](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexists)
- [刪除"不存在同步"](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteifexistsasync)

如果容器不存在，"**刪除**"和 **"刪除Async"** 方法將引發異常。

**DeleteIfExists**和**DeleteIfExistsAsync**方法返回一個布林值，指示容器是否已被刪除。 如果指定的容器不存在，則這些方法返回**False**以指示該容器未被刪除。

刪除容器後，不能創建同名容器至少 30 秒，甚至更長。 刪除容器時，嘗試創建具有相同名稱的容器將失敗，HTTP 錯誤代碼 409（衝突）。 刪除容器時，容器或其包含的 Blob 上的任何其他操作都將失敗，HTTP 錯誤代碼 404（未找到）。

以下示例刪除指定的容器，並在該容器不存在時處理異常：

```csharp
private static async Task DeleteSampleContainerAsync(CloudBlobClient blobClient, string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    try
    {
        // Delete the specified container and handle the exception.
        await container.DeleteAsync();
    }
    catch (StorageException e)
    {
        Console.WriteLine("HTTP error code {0}: {1}",
                            e.RequestInformation.HttpStatusCode,
                            e.RequestInformation.ErrorCode);
        Console.WriteLine(e.Message);
        Console.ReadLine();
    }
}
```

下面的示例演示如何刪除以指定首碼開頭的所有容器。 如果容器上存在現有租約，則該示例將中斷租約。

```csharp
private static async Task DeleteContainersWithPrefixAsync(CloudBlobClient blobClient, string prefix)
{
    Console.WriteLine("Delete all containers beginning with the specified prefix");
    try
    {
        foreach (var container in blobClient.ListContainers(prefix))
        {
            Console.WriteLine("\tContainer:" + container.Name);
            if (container.Properties.LeaseState == LeaseState.Leased)
            {
                await container.BreakLeaseAsync(null);
            }

            await container.DeleteAsync();
        }

        Console.WriteLine();
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [創建容器操作](/rest/api/storageservices/create-container)
- [刪除容器作業](/rest/api/storageservices/delete-container)
