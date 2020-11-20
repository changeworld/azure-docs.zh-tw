---
title: 使用 blob 索引標記來管理和尋找 Azure Blob 儲存體上的資料
description: 請參閱如何使用 blob 索引標記來分類、管理和查詢 blob 物件的範例。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 2e3e16b71d52edd9ab4eaf55651567b95e334b84
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961782"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>使用 blob 索引標記 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料

Blob 索引標記會使用索引鍵/值標記屬性，將儲存體帳戶中的資料分類。 這些標記會自動編制索引，並公開為可搜尋的多維度索引，方便您尋找資料。 本文說明如何使用 Blob 索引標記來設定、取得及尋找資料。

> [!NOTE]
> Blob 索引處於公開預覽狀態，可在 **加拿大中部**、 **加拿大東部**、 **法國中部** 和 **法國南部** 區域中使用。 若要深入瞭解這項功能以及已知問題和限制，請參閱 [使用 Blob 索引標記管理和尋找 Azure blob 資料 (預覽) ](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>Prerequisites

# <a name="portal"></a>[入口網站](#tab/azure-portal)

- 註冊並核准以存取 blob 索引預覽的 Azure 訂用帳戶
- 存取 [Azure 入口網站](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

由於 blob 索引處於預覽狀態，因此會在預覽 NuGet 摘要中發行 .NET 儲存體套件。 此程式庫在預覽期間可能會變更。

1. 設定您的 Visual Studio 專案，以開始使用適用于 .NET 的 Azure Blob 儲存體用戶端程式庫 v12。 若要深入瞭解，請參閱 [.Net 快速入門](storage-quickstart-blobs-dotnet.md)

2. 在 NuGet 封裝管理員中，尋找 **Azure** 12.7.0 套件，並將版本 **-preview. 1** 或更新版本安裝至您的專案。 您也可以執行 PowerShell 命令： `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   若要了解作法，請參閱[尋找並安裝套件](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

3. 在程式碼檔案頂端新增以下 using 陳述式。

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>使用索引標記上傳新的 Blob

這項工作可由 [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 者或透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 自訂 azure 角色授與 [Azure 資源提供者](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 作業許可權的安全性主體來執行。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶 

2. 流覽至 [ **Blob 服務**] 底下的 [**容器**] 選項，選取您的容器

3. 選取 [ **上傳** ] 按鈕，然後流覽您的本機檔案系統，以尋找要上傳為區塊 blob 的檔案。

4. 展開 [進階] 下拉式清單，並移至 [Blob 索引標記] 區段

5. 輸入您想要套用至資料的索引鍵/值 Blob 索引標記

6. 選取 [上傳] 按鈕上傳 Blob

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示如何上傳具有索引標記的 blob。":::

# <a name="net"></a>[.NET](#tab/net)

下列範例示範如何建立附加 Blob，並在建立期間設定標記。

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>取得、設定和更新 Blob 索引標記

[儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者或已透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 自訂 azure 角色授與[Azure 資源提供者](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage)作業許可權的安全性主體，都可以執行 blob 索引標記。

[儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)者或已透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 自訂 azure 角色授與[Azure 資源提供者](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage)作業許可權的安全性主體，都可以執行設定和更新 blob 索引標記。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶 

2. 瀏覽至 [Blob 服務] 底下的 [容器] 選項，然後選取您的容器

3. 從選取之容器內的 blob 清單中選取您的 blob

4. [Blob 概觀] 索引標籤會顯示您的 Blob 屬性，包括任何 **Blob 索引標記**

5. 您可以取得、設定、修改或刪除 Blob 的任何索引鍵/值索引標記

6. 選取 [儲存] 按鈕，以確認您 Blob 的任何更新

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示如何取得、設定、更新和刪除 blob 上的索引標籤。":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>使用 blob 索引標記篩選和尋找資料

這項工作可由 [儲存體 Blob 資料擁有](/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) 者或透過 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 自訂 azure 角色授與 [Azure 資源提供者](/azure/role-based-access-control/resource-provider-operations.md#microsoftstorage) 作業許可權的安全性主體來執行。

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站中，blob 索引標記篩選會自動套用 `@container` 參數，以界定您選取的容器的範圍。 如果您想要在整個儲存體帳戶中篩選和尋找已標記的資料，請使用我們的 REST API、Sdk 或工具。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶。 

2. 流覽至 [ **Blob 服務**] 底下的 [**容器**] 選項，選取您的容器

3. 選取 [Blob 索引標記篩選] 按鈕，以在所選取的容器內進行篩選

4. 輸入 blob 索引標記索引鍵和標記值

5. 選取 [Blob 索引標記篩選] 按鈕，以新增其他標記篩選 (最多 10 個)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示如何使用索引標籤來篩選和尋找已標記的 blob":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>使用 Blob 索引標記篩選的生命週期管理

# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶。 

2. 瀏覽至 [Blob 服務] 底下的 [生命週期管理] 選項

3. 選取 [新增規則]，然後填寫 [動作集] 表單欄位

4. 選取 [ **篩選** 設定] 以新增選擇性篩選準則，以符合前置詞比對和 blob 索引相符

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Azure 入口網站的螢幕擷取畫面，顯示如何新增索引標籤以進行生命週期管理。":::

5. 選取 [ **審核] + [新增** ] 以查看規則設定

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="顯示具有 blob 索引標記篩選範例之生命週期管理規則 Azure 入口網站的螢幕擷取畫面":::

6. 選取 [新增] 以將新規則套用至生命週期管理原則

# <a name="net"></a>[.NET](#tab/net)

[生命週期管理](storage-lifecycle-management-concepts.md)原則會套用至控制平面層級的每個儲存體帳戶。 針對 .NET，請安裝 [Microsoft Azure 管理存放庫](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 版本16.0.0 版或更高版本。

---

## <a name="next-steps"></a>後續步驟

 - 深入瞭解 blob 索引標籤，請參閱 [使用 blob 索引標記管理和尋找 Azure blob 資料 (預覽) ](storage-manage-find-blobs.md )
 - 深入瞭解生命週期管理的詳細資訊，請參閱 [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
