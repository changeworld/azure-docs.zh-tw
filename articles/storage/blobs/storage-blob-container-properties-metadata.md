---
title: 使用 .NET 來管理 blob 容器的屬性和中繼資料
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端程式庫，設定和取出系統屬性，並將自訂中繼資料儲存在 Azure 儲存體帳戶中的 blob 容器上。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c66b521b5cd75825fcafe07b24d5d527c45f5153
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79135916"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>使用 .NET 管理容器屬性和中繼資料

除了其包含的資料以外，Blob 容器還支援系統屬性和使用者定義的中繼資料。 本文說明如何使用[適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)來管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：系統屬性存在於每個 Blob 儲存體資源上。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 適用于 .NET 的 Azure 儲存體用戶端程式庫會為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料是由您為 Blob 儲存體資源指定的一或多個名稱/值配對所組成。 您可以使用中繼資料來儲存資源的額外值。 中繼資料值僅供您自己使用，並不會影響資源的運作方式。

抓取 Blob 儲存體資源的屬性和中繼資料值是兩個步驟的程式。 您必須先呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法明確地擷取這些值，才能開始讀取這些值。 此規則的例外狀況是**Exists**和**ExistsAsync**方法會在幕後呼叫適當的**FetchAttributes**方法。 當您呼叫其中一種方法時，您也不需要呼叫**FetchAttributes**。

> [!IMPORTANT]
> 如果您發現尚未擴展儲存體資源的屬性或中繼資料值，請檢查您的程式碼是否呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法。

中繼資料名稱/值組是有效的 HTTP 標頭，因此應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 c # 識別碼、只能包含 ASCII 字元，而且應該視為不區分大小寫。 包含非 ASCII 字元的中繼資料值應該是以 Base64 編碼或以 URL 編碼。

## <a name="retrieve-container-properties"></a>取得容器屬性

若要取得容器屬性，請呼叫下列其中一個方法：

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [FetchAttributesAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

下列程式碼範例會提取容器的系統屬性，並將一些屬性值寫入主控台視窗：

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

## <a name="set-and-retrieve-metadata"></a>設定和取得中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 若要設定中繼資料，請將名稱/值組加入至資源的**中繼資料**集合，然後呼叫下列其中一種方法來寫入值：

- [SetMetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱會保留其建立時的大小寫，但在設定或讀取時不區分大小寫。 如果為資源提交了兩個或多個具有相同名稱的中繼資料標頭，Blob 儲存體會傳回 HTTP 錯誤碼400（不正確的要求）。

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

## <a name="see-also"></a>請參閱

- [取得容器屬性作業](/rest/api/storageservices/get-container-properties)
- [設定容器中繼資料作業](/rest/api/storageservices/set-container-metadata)
- [取得容器中繼資料作業](/rest/api/storageservices/set-container-metadata)
