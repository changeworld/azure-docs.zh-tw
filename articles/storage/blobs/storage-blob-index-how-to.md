---
title: 利用 Blob 索引來管理和尋找 Azure Blob 儲存體上的資料
description: 請參閱範例，了解如何使用 Blob 索引標記來分類、管理和查詢以探索 Blob 物件。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.openlocfilehash: 6e3ce99211da35105fd9e118a850110dfd48ece1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84986290"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>利用 Blob 索引標記 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料

Blob 索引標記會使用索引鍵/值標記屬性，將儲存體帳戶中的資料分類。 這些標記會自動編製索引，並公開為可查詢的多維度索引，以便輕鬆地尋找資料。 本文說明如何使用 Blob 索引標記來設定、取得及尋找資料。

若要深入了解 Blob 索引，請參閱[使用 Blob 索引 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md)。

> [!NOTE]
> Blob 索引處於公開預覽狀態，並可在**加拿大中部**、**加拿大東部**、**法國中部**和**法國南部**區域中取得。 若要深入了解這項功能以及已知的問題和限制，請參閱[使用 Blob 索引 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md)。

## <a name="prerequisites"></a>Prerequisites
# <a name="portal"></a>[入口網站](#tab/azure-portal)
- 已註冊並核准可存取 Blob 索引預覽的訂用帳戶
- 存取 [Azure 入口網站](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)
由於 Blob 索引處於公開預覽狀態，因此 .NET 儲存體套件會在預覽 NuGet 摘要中發行。 此程式庫在現在和正式發行之間可能會有變更。 

1. 在 Visual Studio 中，將 URL `https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json` 新增至您的 NuGet 套件來源。 

   若要了解作法，請參閱[套件來源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 套件管理員中，尋找 **Azure.Storage.Blobs** 套件，並且將 **12.5.0-dev.20200422.2** 版本安裝至您的專案。 您也可以執行 ```Install-Package Azure.Storage.Blobs -Version 12.5.0-dev.20200422.2``` 命令

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
# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶 

2. 瀏覽至 [Blob 服務] 底下的 [容器] 選項，然後選取您的容器

3. 選取 [上傳] 按鈕以開啟 [上傳] 刀鋒視窗，然後瀏覽您的本機檔案系統，尋找要上傳做為區塊 blob 的檔案。

4. 展開 [進階] 下拉式清單，並移至 [Blob 索引標記] 區段

5. 輸入您想要套用至資料的索引鍵/值 Blob 索引標記

6. 選取 [上傳] 按鈕上傳 Blob

![使用 Blob 索引標記上傳資料](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

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
         
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
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
# <a name="portal"></a>[入口網站](#tab/azure-portal)

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶 

2. 瀏覽至 [Blob 服務] 底下的 [容器] 選項，然後選取您的容器

3. 從所選容器內的 Blob 清單中選取您想要的 Blob

4. [Blob 概觀] 索引標籤會顯示您的 Blob 屬性，包括任何 **Blob 索引標記**

5. 您可以取得、設定、修改或刪除 Blob 的任何索引鍵/值索引標記

6. 選取 [儲存] 按鈕，以確認您 Blob 的任何更新

![取得、設定、更新和刪除物件上的 Blob 索引標記](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

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

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>使用 Blob 索引標記篩選和尋找資料

# <a name="portal"></a>[入口網站](#tab/azure-portal)

在 Azure 入口網站內，Blob 索引標記篩選會自動套用 `@container` 參數，將範圍限制為所選容器。 如果您想要在整個儲存體帳戶中篩選並尋找已標記的資料，請使用我們的 REST API、SDK 或工具。

1. 在 [Azure 入口網站](https://portal.azure.com/)中，選取您的儲存體帳戶。 

2. 瀏覽至 [Blob 服務] 底下的 [容器] 選項，然後選取您的容器

3. 選取 [Blob 索引標記篩選] 按鈕，以在所選取的容器內進行篩選

4. 輸入 Blob 索引標記索引鍵和標記值

5. 選取 [Blob 索引標記篩選] 按鈕，以新增其他標記篩選 (最多 10 個)

![使用 Blob 索引標記篩選和尋找已標記物件](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
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
           
          // Blob Index tags to upload
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

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
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

4. 選取篩選設定以新增前置詞比對和 Blob 索引比對的選擇性篩選![新增用於生命週期管理的 Blob 索引標記篩選](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. 選取 [檢閱 + 新增]，以檢閱規則設定![使用 Blob 索引標記篩選的生命週期管理規則範例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. 選取 [新增] 以將新規則套用至生命週期管理原則

# <a name="net"></a>[.NET](#tab/net)
[生命週期管理](storage-lifecycle-management-concepts.md)原則會套用至控制平面層級的每個儲存體帳戶。 針對 .NET，請安裝 [Microsoft Azure 管理儲存體程式庫 16.0.0 版](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/)或更新版本，以利用生命週期管理規則中的 Blob 索引比對篩選。

---

## <a name="next-steps"></a>後續步驟

深入了解 Blob 索引。 請參閱[利用 Blob 索引 (預覽) 來管理和尋找 Azure Blob 儲存體上的資料](storage-manage-find-blobs.md )

深入了解生命週期管理。 請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
