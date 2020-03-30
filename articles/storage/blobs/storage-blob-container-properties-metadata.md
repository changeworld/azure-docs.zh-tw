---
title: 使用 .NET 管理 Blob 容器的屬性和中繼資料
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端庫在 Azure 存儲帳戶中的 Blob 容器上設置和檢索系統屬性和存儲自訂中繼資料。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135916"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>使用 .NET 管理容器屬性和中繼資料

Blob 容器除了支援它們包含的資料外，還支援系統屬性和使用者定義的中繼資料。 本文演示如何使用 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：每個 Blob 存儲資源上都存在系統屬性。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 .NET 的 Azure 存儲用戶端庫為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料由為 Blob 存儲資源指定的一個或多個名稱值對組成。 可以使用中繼資料將其他值與資源一起存儲。 中繼資料值僅供您自己使用，並不會影響資源的運作方式。

檢索 Blob 存儲資源的屬性和中繼資料值是一個兩步過程。 您必須先呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法明確地擷取這些值，才能開始讀取這些值。 此規則的例外情況是，"**存在**"和 **"存在 Async"** 方法在封面下調用相應的**FetchAttributes**方法。 調用這些方法之一時，不需要也調用**FetchAttributes**。

> [!IMPORTANT]
> 如果您發現尚未擴展儲存體資源的屬性或中繼資料值，請檢查您的程式碼是否呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法。

中繼資料名稱/值對是有效的 HTTP 標頭，因此應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 C# 識別碼，可能僅包含 ASCII 字元，並且應視為不區分大小寫。 包含非 ASCII 字元的中繼資料值應為 Base64 編碼或 URL 編碼。

## <a name="retrieve-container-properties"></a>檢索容器屬性

要檢索容器屬性，請調用以下方法之一：

- [提取屬性](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [提取屬性同步](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

以下代碼示例獲取容器的系統屬性並將一些屬性值寫入主控台視窗：

```csharp
private static async Task ReadContainerPropertiesAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch some container properties and write out their values.
        await container.FetchAttributesAsync();
        Console.WriteLine("Properties for container {0}", container.StorageUri.PrimaryUri);
        Console.WriteLine("Public access level: {0}", container.Properties.PublicAccess);
        Console.WriteLine("Last modified time in UTC: {0}", container.Properties.LastModified);
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

## <a name="set-and-retrieve-metadata"></a>設置和檢索中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 要設置中繼資料，請向資源上的**中繼資料**集合添加名稱-值對，然後調用以下方法之一來編寫值：

- [設置中繼資料](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [設置元點同步](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱保留創建它們的情況，但在設置或讀取時不區分大小寫。 如果為資源提交兩個或多個具有相同名稱的中繼資料標頭，Blob 存儲將返回 HTTP 錯誤代碼 400（錯誤請求）。

下列程式碼範例會在容器上設定中繼資料。 其中一個值是使用集合的 **Add** 方法設定。 其他值是使用隱含的索引鍵/值語法來設定。 兩者都有效。

```csharp
public static async Task AddContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Add some metadata to the container.
        container.Metadata.Add("docType", "textDocuments");
        container.Metadata["category"] = "guidance";

        // Set the container's metadata.
        await container.SetMetadataAsync();
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

若要擷取 Blob 或容器的中繼資料，請呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法以填入**中繼資料**集合，然後讀取這些值，如以下範例所示。

```csharp
public static async Task ReadContainerMetadataAsync(CloudBlobContainer container)
{
    try
    {
        // Fetch container attributes in order to populate the container's properties and metadata.
        await container.FetchAttributesAsync();

        // Enumerate the container's metadata.
        Console.WriteLine("Container metadata:");
        foreach (var metadataItem in container.Metadata)
        {
            Console.WriteLine("\tKey: {0}", metadataItem.Key);
            Console.WriteLine("\tValue: {0}", metadataItem.Value);
        }
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

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [獲取容器屬性操作](/rest/api/storageservices/get-container-properties)
- [設置容器中繼資料操作](/rest/api/storageservices/set-container-metadata)
- [獲取容器中繼資料操作](/rest/api/storageservices/set-container-metadata)
