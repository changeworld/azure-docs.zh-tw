---
title: 使用 .NET - Azure 存儲管理 Blob 的屬性和中繼資料
description: 瞭解如何使用 .NET 用戶端庫設置和檢索系統屬性，並在 Azure 存儲帳戶中的 blob 上存儲自訂中繼資料。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137657"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>使用 .NET 管理 blob 屬性和中繼資料

除了它們包含的資料外，blob 還支援系統屬性和使用者定義的中繼資料。 本文演示如何使用 .NET 的[Azure 存儲用戶端庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：每個 Blob 存儲資源上都存在系統屬性。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 .NET 的 Azure 存儲用戶端庫為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料由為 Blob 存儲資源指定的一個或多個名稱值對組成。 可以使用中繼資料將其他值與資源一起存儲。 中繼資料值僅供您使用，不會影響資源如何操作。

檢索 Blob 存儲資源的中繼資料和屬性值是一個兩步過程。 在讀取這些值之前，必須通過調用`FetchAttributes`或`FetchAttributesAsync`方法顯式獲取它們。 此規則的例外情況是 ，`Exists`和`ExistsAsync`方法在封面下`FetchAttributes`調用適當的方法。 調用這些方法之一時，不需要也調用`FetchAttributes`。

> [!IMPORTANT]
> 如果發現尚未填充存儲資源的屬性或中繼資料值，請檢查代碼是否調用 或`FetchAttributes``FetchAttributesAsync`方法。

## <a name="set-and-retrieve-properties"></a>設置和檢索屬性

以下代碼示例設置 Blob `ContentType` `ContentLanguage`上的 和 系統屬性。

```csharp
public static async Task SetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        Console.WriteLine("Setting blob properties.");

        // You must explicitly set the MIME ContentType every time
        // the properties are updated or the field will be cleared.
        blob.Properties.ContentType = "text/plain";
        blob.Properties.ContentLanguage = "en-us";

        // Set the blob's properties.
        await blob.SetPropertiesAsync();
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

若要檢索 blob 屬性，`FetchAttributes`請`FetchAttributesAsync`調用 blob 上的 或`Properties`方法以填充該屬性。 以下代碼示例獲取 blob 的系統屬性並顯示一些值：

```csharp
private static async Task GetBlobPropertiesAsync(CloudBlob blob)
{
    try
    {
        // Fetch the blob properties.
        await blob.FetchAttributesAsync();

        // Display some of the blob's property values.
        Console.WriteLine(" ContentLanguage: {0}", blob.Properties.ContentLanguage);
        Console.WriteLine(" ContentType: {0}", blob.Properties.ContentType);
        Console.WriteLine(" Created: {0}", blob.Properties.Created);
        Console.WriteLine(" LastModified: {0}", blob.Properties.LastModified);
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

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 要設置中繼資料，向資源上`Metadata`的集合添加名稱值對。 然後，調用以下方法之一來編寫值：

- [設置中繼資料](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [設置元點同步](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

中繼資料名稱/值對是有效的 HTTP 標頭，應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 C# 識別碼，可能僅包含 ASCII 字元，並且應視為不區分大小寫。 包含非 ASCII 字元[的 Base64 編碼](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string)或[URL 編碼](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode)中繼資料值。

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱維護創建時使用的情況，但在設置或讀取時不區分大小寫。 如果為資源提交兩個或多個具有相同名稱的中繼資料標頭，Azure Blob 存儲將返回 HTTP 錯誤代碼 400（錯誤請求）。

以下代碼示例在 Blob 上設置中繼資料。 使用集合`Add`的方法設置一個值。 其他值是使用隱含的索引鍵/值語法來設定。

```csharp
public static async Task AddBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Add metadata to the blob by calling the Add method.
        blob.Metadata.Add("docType", "textDocuments");

        // Add metadata to the blob by using key/value syntax.
        blob.Metadata["category"] = "guidance";

        // Set the blob's metadata.
        await blob.SetMetadataAsync();
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

若要檢索中繼資料，請調用`FetchAttributes` `FetchAttributesAsync` blob 或容器上的 or 方法`Metadata`來填充集合，然後讀取值，如下例所示。

```csharp
public static async Task ReadBlobMetadataAsync(CloudBlob blob)
{
    try
    {
        // Fetch blob attributes in order to populate 
        // the blob's properties and metadata.
        await blob.FetchAttributesAsync();

        Console.WriteLine("Blob metadata:");

        // Enumerate the blob's metadata.
        foreach (var metadataItem in blob.Metadata)
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

- [設置 Blob 屬性操作](/rest/api/storageservices/set-blob-properties)
- [獲取 Blob 屬性操作](/rest/api/storageservices/get-blob-properties)
- [設置 Blob 中繼資料操作](/rest/api/storageservices/set-blob-metadata)
- [獲取 Blob 中繼資料操作](/rest/api/storageservices/get-blob-metadata)
