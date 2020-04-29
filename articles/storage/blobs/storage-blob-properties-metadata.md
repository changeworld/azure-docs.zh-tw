---
title: 使用 .NET 管理 blob 的屬性和中繼資料-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫，設定和取出系統屬性，並將自訂中繼資料儲存在 Azure 儲存體帳戶中的 blob 上。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 08/09/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: b4abd7e29dec67ddc1be50a2a6703da2a25551d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79137657"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>使用 .NET 管理 blob 屬性和中繼資料

除了其包含的資料以外，blob 還支援系統屬性和使用者定義的中繼資料。 本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：系統屬性存在於每個 Blob 儲存體資源上。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 適用于 .NET 的 Azure 儲存體用戶端程式庫會為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料是由您為 Blob 儲存體資源指定的一或多個名稱/值配對所組成。 您可以使用中繼資料來儲存資源的額外值。 中繼資料值僅供您自己的用途，不會影響資源的運作方式。

抓取 Blob 儲存體資源的中繼資料和屬性值是兩個步驟的程式。 在您可以讀取這些值之前，您必須呼叫`FetchAttributes`或`FetchAttributesAsync`方法明確地提取它們。 此規則的例外狀況是`Exists`和`ExistsAsync`方法會在幕後呼叫適當`FetchAttributes`的方法。 當您呼叫其中一種方法時，您也不需要呼叫`FetchAttributes`。

> [!IMPORTANT]
> 如果您發現尚未填入儲存體資源的屬性或中繼資料值，請檢查您的程式碼是否呼叫`FetchAttributes`或`FetchAttributesAsync`方法。

## <a name="set-and-retrieve-properties"></a>設定和取出屬性

下列程式碼範例會在`ContentType` blob `ContentLanguage`上設定和系統屬性。

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

若要抓取 blob 屬性，請`FetchAttributes`在`FetchAttributesAsync`您的 blob 上呼叫或方法`Properties` ，以填入屬性。 下列程式碼範例會取得 blob 的系統屬性，並顯示一些值：

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

## <a name="set-and-retrieve-metadata"></a>設定和取得中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 若要設定中繼資料，請將名稱/值`Metadata`組加入至資源上的集合。 然後，呼叫下列其中一種方法來寫入值：

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)

中繼資料名稱/值組是有效的 HTTP 標頭，且應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 c # 識別碼、只能包含 ASCII 字元，而且應該視為不區分大小寫。 [Base64 編碼](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string)或[URL 編碼的](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode)中繼資料值，其中包含非 ASCII 字元。

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱會維持建立時所使用的大小寫，但在設定或讀取時不區分大小寫。 如果為資源提交了兩個或多個使用相同名稱的中繼資料標頭，Azure Blob 儲存體會傳回 HTTP 錯誤碼400（不正確的要求）。

下列程式碼範例會在 blob 上設定中繼資料。 其中一個值是使用集合的`Add`方法來設定。 其他值是使用隱含的索引鍵/值語法來設定。

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

若要取得中繼資料， `FetchAttributes`請`FetchAttributesAsync`在您的 blob 或容器上呼叫或`Metadata`方法來填入集合，然後讀取值，如下列範例所示。

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

## <a name="see-also"></a>請參閱

- [設定 Blob 屬性作業](/rest/api/storageservices/set-blob-properties)
- [取得 Blob 屬性作業](/rest/api/storageservices/get-blob-properties)
- [設定 Blob 中繼資料作業](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob 中繼資料作業](/rest/api/storageservices/get-blob-metadata)
