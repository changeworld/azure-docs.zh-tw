---
title: 使用 .NET 管理 blob 的屬性和中繼資料-Azure 儲存體
description: 瞭解如何使用 .NET 用戶端程式庫，來設定和取出系統屬性，並將自訂中繼資料儲存在您 Azure 儲存體帳戶中的 blob 上。
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/25/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 19d20a208672667e5a4354fd1b7d185d0c00f8d9
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399120"
---
# <a name="manage-blob-properties-and-metadata-with-net"></a>使用 .NET 管理 blob 屬性和中繼資料

除了所包含的資料之外，blob 還支援系統屬性和使用者定義的中繼資料。 本文說明如何使用 [適用于 .net 的 Azure 儲存體用戶端程式庫](/dotnet/api/overview/azure/storage)，管理系統屬性和使用者定義的中繼資料。

## <a name="about-properties-and-metadata"></a>關於屬性和中繼資料

- **系統屬性**：系統屬性存在於每個 Blob 儲存體資源上。 其中有些系統屬性可以讀取或設定，有些則是唯讀的。 實際上，有些系統屬性會對應至特定的標準 HTTP 標頭。 適用于 .NET 的 Azure 儲存體用戶端程式庫會為您維護這些屬性。

- **使用者定義的中繼資料**：使用者定義的中繼資料是由您為 Blob 儲存體資源指定的一或多個名稱/值配對所組成。 您可以使用中繼資料來儲存其他值與資源。 中繼資料值僅供您自己使用，且不會影響資源的運作方式。

> [!NOTE]
> Blob 索引標記也提供將任意使用者定義索引鍵/值屬性與 Azure Blob 儲存體資源一起儲存的功能。 與中繼資料類似的是，只會自動編制 blob 索引標記的索引，並由原生 blob 服務進行搜尋。 除非您使用不同的服務（例如 Azure 搜尋服務），否則無法編制中繼資料的索引和查詢。
>
> 若要深入瞭解這項功能，請參閱 [在 Azure blob 儲存體上使用 Blob 索引 (預覽) 管理和尋找資料 ](storage-manage-find-blobs.md)。

## <a name="set-and-retrieve-properties"></a>設定和取出屬性

下列程式碼範例會 `ContentType` `ContentLanguage` 在 blob 上設定和系統屬性。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要設定 blob 的屬性，請呼叫 [SetHttpHeaders](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheaders) 或 [SetHttpHeadersAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.sethttpheadersasync)。 未明確設定的任何屬性都會被清除。 下列程式碼範例會先取得 blob 上現有的屬性，然後使用它們來填入未更新的標頭。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_SetBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

下列程式碼範例會取得 blob 的系統屬性，並顯示部分值。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobProperties":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

您可以使用兩個步驟的程式，來抓取 Blob 儲存體資源的中繼資料和屬性值。 在您可以讀取這些值之前，您必須藉由呼叫或方法明確地提取它們 `FetchAttributes` `FetchAttributesAsync` 。 這項規則的例外狀況是 `Exists` ，和 `ExistsAsync` 方法會在幕後呼叫適當的 `FetchAttributes` 方法。 當您呼叫其中一個方法時，您也不需要呼叫 `FetchAttributes` 。

> [!IMPORTANT]
> 如果您發現儲存體資源的屬性或中繼資料值尚未填入，請檢查您的程式碼是否呼叫 `FetchAttributes` 或 `FetchAttributesAsync` 方法。

若要取得 blob 屬性，請 `FetchAttributes` `FetchAttributesAsync` 在 blob 上呼叫或方法來填入 `Properties` 屬性。

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
---

## <a name="set-and-retrieve-metadata"></a>設定和取出中繼資料

您可以針對 Blob 或容器資源，將中繼資料指定為一個或多個名稱/值組。 若要設定中繼資料，請將名稱-值組加入至 `Metadata` 資源上的集合。 然後，呼叫下列其中一個方法來寫入值：

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

- [>setmetadata](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadata)
- [SetMetadataAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.setmetadataasync)

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

- [>setmetadata](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadata)
- [SetMetadataAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.setmetadataasync)
---

中繼資料名稱/值組是有效的 HTTP 標頭，且應遵守管理 HTTP 標頭的所有限制。 中繼資料名稱必須是有效的 HTTP 標頭名稱和有效的 c # 識別碼，且只能包含 ASCII 字元，且應該視為不區分大小寫。 [Base64 編碼](https://docs.microsoft.com/dotnet/api/system.convert.tobase64string) 或 [URL 編碼的](https://docs.microsoft.com/dotnet/api/system.web.httputility.urlencode) 中繼資料值，包含非 ASCII 字元。

您的中繼資料名稱必須符合 C# 識別碼的命名慣例。 中繼資料名稱會維護建立時所使用的大小寫，但在設定或讀取時不區分大小寫。 如果有兩個或多個使用相同名稱的中繼資料標頭提交給資源，Azure Blob 儲存體會傳回 HTTP 錯誤碼 400 (錯誤的要求) 。

下列程式碼範例會在 blob 上設定中繼資料。 您可以使用集合的方法來設定一個值 `Add` 。 其他值是使用隱含的索引鍵/值語法來設定。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_AddBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

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
---

下列程式碼範例會讀取 blob 的中繼資料。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

若要取得中繼資料，請在 blob 或容器上呼叫 [GetProperties](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getproperties) 或 [GetPropertiesAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.getpropertiesasync) 方法來填入 [中繼資料](/dotnet/api/azure.storage.blobs.models.blobproperties.metadata) 集合，然後讀取這些值，如下列範例所示。 **GetProperties**方法會在單一呼叫中取出 blob 屬性和中繼資料。 這不同于需要個別呼叫來 [取得 Blob 屬性](/rest/api/storageservices/get-blob-properties) 和 [取得 blob 中繼資料](/rest/api/storageservices/get-blob-metadata)的 REST api。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Metadata.cs" id="Snippet_ReadBlobMetadata":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

若要取得中繼資料， `FetchAttributes` 請 `FetchAttributesAsync` 在 blob 或容器上呼叫或方法以填入 `Metadata` 集合，然後讀取這些值，如下列範例所示。

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
---

[!INCLUDE [storage-blob-dotnet-resources-include](../../../includes/storage-blob-dotnet-resources-include.md)]

## <a name="see-also"></a>另請參閱

- [設定 Blob 屬性作業](/rest/api/storageservices/set-blob-properties)
- [取得 Blob 屬性作業](/rest/api/storageservices/get-blob-properties)
- [設定 Blob 中繼資料作業](/rest/api/storageservices/set-blob-metadata)
- [取得 Blob 中繼資料作業](/rest/api/storageservices/get-blob-metadata)
