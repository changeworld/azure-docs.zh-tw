---
title: 使用 .NET 來管理 blob 容器的屬性和中繼資料
titleSuffix: Azure Storage
description: 瞭解如何使用 .NET 用戶端程式庫，來設定和取出系統屬性，並將自訂中繼資料儲存在您 Azure 儲存體帳戶中的 blob 容器上。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/01/2020
ms.author: tamram
ms.custom: devx-track-csharp
ms.openlocfilehash: 115ab9767c23afd5876eb23821bd1283777cdb5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018921"
---
# <a name="manage-container-properties-and-metadata-with-net"></a>使用 .NET 管理容器屬性和中繼資料

除了所包含的資料之外，Blob 容器還支援系統屬性和使用者定義的中繼資料。 本文說明如何使用 [適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage?view=azure-dotnet)，管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：系統屬性存在於每個 Blob 儲存體資源上。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 適用于 .NET 的 Azure 儲存體用戶端程式庫會為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料是由您為 Blob 儲存體資源指定的一或多個名稱/值配對所組成。 您可以使用中繼資料來儲存其他值與資源。 中繼資料值僅供您自己使用，並不會影響資源的運作方式。

中繼資料名稱/值組是有效的 HTTP 標頭，因此應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 c # 識別碼，且只能包含 ASCII 字元，且應該視為不區分大小寫。 包含非 ASCII 字元的中繼資料值應為 Base64 編碼或 URL 編碼。

## <a name="retrieve-container-properties"></a>取出容器屬性

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要取出容器屬性，請呼叫下列其中一種方法：

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)

下列程式碼範例會提取容器的系統屬性，並將一些屬性值寫入主控台視窗：

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

抓取 Blob 儲存體資源的屬性和中繼資料值是一個兩步驟的程式。 您必須先呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法明確地擷取這些值，才能開始讀取這些值。 這項規則的例外狀況是 **Exists** 和 **ExistsAsync** 方法會在幕後呼叫適當的 **FetchAttributes** 方法。 當您呼叫其中一個方法時，您也不需要呼叫 **FetchAttributes**。

> [!IMPORTANT]
> 如果您發現尚未擴展儲存體資源的屬性或中繼資料值，請檢查您的程式碼是否呼叫 **FetchAttributes** 或 **FetchAttributesAsync** 方法。

若要取出容器屬性，請呼叫下列其中一種方法：

- [FetchAttributes](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributes)
- [>fetchattributesasync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.fetchattributesasync)

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

---

## <a name="set-and-retrieve-metadata"></a>設定和取出中繼資料

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 若要設定中繼資料，請將名稱-值組加入至 [IDictionary](/dotnet/api/system.collections.idictionary) 物件，然後呼叫下列其中一種方法來寫入值：

- [>setmetadata](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.setmetadataasync)

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱會保留建立它們的大小寫，但在設定或讀取時，不區分大小寫。 如果為資源提交兩個以上具有相同名稱的中繼資料標頭，Blob 儲存體會以逗號分隔並串連這兩個值，並傳回 HTTP 回應碼 200 (確定) 。

下列程式碼範例會在容器上設定中繼資料。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddContainerMetadata":::

若要取出中繼資料，請呼叫下列其中一種方法：

- [GetProperties](/dotnet/api/azure.storage.blobs.blobcontainerclient.getproperties)
- [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getpropertiesasync)。

然後，讀取這些值，如下列範例所示。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadContainerMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 若要設定中繼資料，請將名稱-值組加入至資源上的 **中繼資料** 集合，然後呼叫下列其中一種方法來寫入值：

- [>setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.setmetadataasync)

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱會保留建立它們的大小寫，但在設定或讀取時，不區分大小寫。 如果為資源提交兩個以上具有相同名稱的中繼資料標頭，Blob 儲存體會以逗號分隔並串連這兩個值，並傳回 HTTP 回應碼 200 (確定) 。

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

---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [取得容器屬性操作](/rest/api/storageservices/get-container-properties)
- [設定容器中繼資料作業](/rest/api/storageservices/set-container-metadata)
- [取得容器中繼資料作業](/rest/api/storageservices/get-container-metadata)
