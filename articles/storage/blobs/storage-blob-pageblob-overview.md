---
title: Azure 分頁 Blob 的概觀 | Microsoft Docs
description: Azure 分頁 Blob 和其優點的概觀，包括具有範例指令碼的使用案例。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: f54adb54ca842ea389b0d3ea203d747df0071ee5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84792025"
---
# <a name="overview-of-azure-page-blobs"></a>Azure 分頁 Blob 的概觀

Azure 儲存體提供三種類型的 Blob 儲存體：區塊 Blob、附加 Blob 及分頁 Blob。 區塊 blob 是由區塊所組成，適合用來儲存文字或二進位檔案，以及有效率地上傳大型檔案。 附加 Blob 也是由區塊所組成，但是它們是針對附加作業最佳化，使其適合用於記錄案例。 分頁 Blob 是由大小上限為 8 TB 的 512 位元組分頁所組成，專為頻繁的隨機讀取/寫入作業而設計。 分頁 Blob 是 Azure IaaS 磁碟的基礎。 本文著重於說明分頁 Blob 的功能和優點。

分頁 Blob 是 512 位元組分頁的集合，可提供讀取/寫入任意位元組範圍的功能。 因此，分頁 Blob 適合用於儲存索引式和疏鬆檔案結構，例如虛擬機器和資料庫的 OS 和資料磁碟。 例如，Azure SQL DB 會使用分頁 Blob 作為其資料庫的基礎永續性儲存體。 此外，分頁 Blob 也經常用於具有範圍型更新的檔案。  

Azure 分頁 Blob 的主要功能包括其 REST 介面、基礎儲存體的持久性，以及能順暢移轉至 Azure 的能力。 下一節將更深入討論這些功能。 此外，Azure 分頁 Blob 目前支援兩種類型的儲存體：進階儲存體和標準儲存體。 進階儲存體是特別針對需要一致高效能和低延遲的工作負載而設計，讓 premium 分頁 blob 適用于高效能儲存案例。 標準儲存體帳戶較符合成本效益，可執行不區分延遲的工作負載。

## <a name="sample-use-cases"></a>範例使用案例

讓我們來討論一些分頁 Blob 的使用案例，從 Azure IaaS 磁碟開始。 Azure 分頁 Blob 是 Azure IaaS 之虛擬磁碟平台的骨幹。 Azure OS 和資料磁碟皆會以虛擬磁碟的形式實作，其中資料會永久保存在 Azure 儲存體平台中，然後傳遞到虛擬機器以獲得最大效能。 Azure 磁碟會以 Hyper-V [VHD 格式](https://technet.microsoft.com/library/dd979539.aspx)保存，並在 Azure 儲存體中儲存為[分頁 Blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs)。 除了針對 Azure IaaS VM 使用虛擬磁碟以外，分頁 Blob 也能提供 PaaS 和 DBaaS 案例 (例如 Azure SQL DB 服務)，這些案例目前將分頁 Blob 用於儲存 SQL 資料，來為資料庫提供快速的隨機讀取-寫入作業。 另一個範例是如果您具有針對協力視訊編輯應用程式之共用媒體存取的 PaaS 服務，分頁 Blob 將可提供針對媒體中隨機位置的快速存取。 它也可讓多個使用者快速且有效率地編輯與合併相同的媒體。 

第一方 Microsoft 服務 (例如 Azure Site Recovery、Azure 備份)，以及許多第三方協力廠商的開發人員，皆使用分頁 Blob 的 REST 介面來實作領先業界的創新功能。 以下是一些實作於 Azure 上的獨特案例： 

* 應用程式導向的累加快照集管理：應用程式可以利用分頁 Blob 快照集和 REST API 來儲存應用程式檢查點，以免於因複製資料所帶來高成本。 Azure 儲存體支援針對分頁 Blob 的本機快照集，此功能並不需要複製整個 Blob。 這些公用快照集 API 也能提供針對快照集間差異的存取及複製。
* 從內部部署到雲端的應用程式和資料即時移轉：複製內部部署資料，並使用 REST Api 直接寫入 Azure 分頁 blob，同時內部部署 VM 會繼續執行。 一旦趕上目標之後，您可以使用該資料快速地容錯移轉至 Azure VM。 如此一來，您就可以將 Vm 和虛擬磁片從內部部署遷移至雲端，但停機時間最短，因為資料移轉會在背景中進行，而當您繼續使用 VM 時，容錯移轉所需的停機時間會很短（以分鐘為單位）。
* [SAS 型](../common/storage-sas-overview.md)共用存取，可實現如多個讀取器及單一寫入器並支援並行控制的案例。

## <a name="page-blob-features"></a>分頁 Blob 功能

### <a name="rest-api"></a>REST API

請參閱下列文件以開始[使用分頁 Blob 進行開發](storage-dotnet-how-to-use-blobs.md)。 例如，看一下如何使用適用於 .NET 的儲存體用戶端程式庫來存取分頁 Blob。 

下圖說明帳戶、容器和分頁 Blob 之間的整體關聯性。

![顯示帳戶、容器和分頁 blob 之間關聯性的螢幕擷取畫面](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>建立指定大小的空分頁 Blob

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

首先，取得容器的參考。 若要建立分頁 blob，請呼叫[GetPageBlobClient](/dotnet/api/azure.storage.blobs.specialized.specializedblobextensions.getpageblobclient)方法，然後呼叫[PageBlobClient. create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create)方法。 傳入要建立之 blob 的大小上限。 該大小必須是512個位元組的倍數。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要建立分頁 blob，我們會先建立**CloudBlobClient**物件，並使用基底 URI 來存取儲存體帳戶的 blob 儲存體（如 [圖 1] 中的*Pbaccount* ）以及**StorageCredentialsAccountAndKey**物件，如下列範例所示。 然後，此範例會顯示如何建立**CloudBlobContainer**物件的參考，然後建立容器（*testvhds*）（如果尚未存在的話）。 然後，透過 **CloudBlobContainer** 物件，建立 **CloudPageBlob** 物件的參照，方法是指定要存取的分頁 Blob 名稱 (os4.vhd)。 若要建立分頁 blob，請呼叫[CloudPageBlob](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create)，傳入要建立之 blob 的大小上限。 *blobSize* 必須是 512 位元組的倍數。

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>調整分頁 Blob 大小

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要在建立之後調整分頁 blob 的大小，請使用重[設大小](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize?view=azure-dotnet)方法。 要求的大小應該是 512 位元組的倍數。

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要在建立之後調整分頁 blob 的大小，請使用重[設大小](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize)方法。 要求的大小應該是 512 位元組的倍數。

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>將分頁寫入分頁 Blob

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要寫入頁面，請使用[PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages)方法。  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要寫入分頁，請使用 [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages) 方法。  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

這可讓您寫入最多 4MB 的連續分頁集合。 要寫入的位移必須從 512 位元組界限 (startingOffset % 512 == 0) 開始，並於 512 界限 - 1 的位置結束。 

一旦連續分頁集合的寫入要求在 Blob 服務中成功，並已針對持久性和恢復性完成複寫，則寫入便已認可，並會將成功狀態傳回至用戶端。  

下圖說明 2 個獨立的寫入作業：

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  長度為 1024 位元組並從位移 0 開始的寫入作業 
2.  長度為 1024 位元組並從位移 4096 開始的寫入作業 

#### <a name="reading-pages-from-a-page-blob"></a>從分頁 Blob 讀取分頁

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要讀取頁面，請使用[PageBlobClient](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download)方法來讀取分頁 blob 中的位元組範圍。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要讀取分頁，請使用 [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) 方法，從分頁 Blob 讀取位元組範圍。 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

這可讓您從 Blob 中的任何位移開始，下載完整的 Blob 或位元組範圍。 讀取時，位移並不需要從 512 的倍數開始。 從 NUL 分頁讀取位元組時，服務會傳回零個位元組。

下圖顯示位移為256且範圍大小為4352的讀取作業。 傳回的資料會以橙色反白顯示。 為 NUL 頁面傳回零。

![](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

如果您有疏鬆填入的 Blob，建議只下載有效的頁面區域，以避免支付零位元組的輸出，並降低下載延遲。  

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

若要判斷哪些頁面是由資料所支援，請使用[PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges)。 然後，您可以對傳回的範圍進行列舉，並下載每個範圍中的資料。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

若要判斷有哪些分頁是由資料所支援，請使用 [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges)。 然後，您可以對傳回的範圍進行列舉，並下載每個範圍中的資料。 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>租用分頁 Blob

租用 Blob 作業會針對寫入和刪除作業，在 Blob 上建立鎖定並加以管理。 這項作業在有多個用戶端存取分頁 Blob 的案例中很有用，因為它可確保一次僅有一個用戶端可以寫入 Blob。 例如，Azure 磁碟會運用此租用機制來確保磁碟僅受單一 VM 管理。 鎖定持續時間的範圍是 15 到 60 秒，也可以設為無限。 如需詳細資料，請參閱[這裡](/rest/api/storageservices/lease-blob)的文件。

除了豐富的 REST Api，分頁 blob 也提供共用存取、耐久性和增強的安全性。 我們將會在接下來的段落中詳細介紹那些優點。 

### <a name="concurrent-access"></a>並行存取

分頁 Blob REST API 和其租用機制，允許應用程式從多個用戶端存取分頁 Blob。 例如，假設您要建置可與多個使用者共用儲存體物件的分散式雲端服務。 這可能是能為數個使用者提供大型映像集合的 Web 應用程式。 實作此項目的其中一個選項，是使用搭配已連接磁碟的 VM。 此作法的缺點包括：(i) 磁碟僅能連接至單一 VM 的約束，限制了延展性及彈性，並使風險提升。 如果 VM 或於 VM 上執行的服務發生問題，則因為租用的緣故，在租用到期或者中斷之前，映像會無法存取；此外，另一個缺點則是 (ii) 因擁有 IaaS VM 而產生的額外成本。 

替代選項是直接透過 Azure 儲存體 REST API 使用分頁 Blob。 此選項不需要高成本的 IaaS VM、提供從多個用戶端進行直接存取的完整彈性、透過消除連接/卸除磁碟的需求來簡化傳統部署模型，並消除 VM 上發生問題的風險。 另外，它也能針對隨機讀取/寫入作業，提供和磁碟相同的效能等級

### <a name="durability-and-high-availability"></a>持久性和高可用性

標準儲存體和進階儲存體皆為持久的儲存體，它們一律會複寫分頁 Blob 資料以確保持久性和高可用性。 如需有關 Azure 儲存體備援的詳細資訊，請參閱此[文件](../common/storage-redundancy.md)。 Azure 已持續為 IaaS 磁片和分頁 blob 提供企業級持久性，並具有領先業界的零[年度失敗率](https://en.wikipedia.org/wiki/Annualized_failure_rate)。

### <a name="seamless-migration-to-azure"></a>順暢地遷移至 Azure

針對想要自行實作自訂備份解決方案的客戶和開發人員，Azure 也提供僅保留差異的增量快照集。 此功能可以避免由初始完整複製所帶來的成本，並能大幅降低備份成本。 它還能有效地讀取及複製差異資料，此強大功能可為開發人員促成更多的創新，進而在 Azure 上帶來業界最佳的備份與災害復原 (DR) 體驗。 您可以搭配[取得分頁範圍](/rest/api/storageservices/get-page-ranges) API 和[累加複製 Blob](/rest/api/storageservices/incremental-copy-blob) API 使用 [Blob 快照集](/rest/api/storageservices/snapshot-blob)，在 Azure 上針對 VM 設定您自己的備份或 DR 解決方案，並使用它們來輕鬆複製 DR 的增量資料。 

此外，許多企業在內部部署資料中心上皆有正在執行中的關鍵工作負載。 若要將工作負載移轉至雲端，其中一個主要考量就是複製資料所需的停機時間量，還有轉換後發生未預期問題的風險。 在許多情況下，停機時間的考量會使許多人打消移轉至雲端的念頭。 藉由使用分頁 Blob REST API，Azure 得以在對關鍵工作負載產生最小影響的情況下完成雲端移轉，來解決此問題。 

如需如何擷取快照集，以及如何從快照集還原分頁 Blob 的範例，請參閱[使用累加快照集設定備份程序](../../virtual-machines/windows/incremental-snapshots.md)一文。
