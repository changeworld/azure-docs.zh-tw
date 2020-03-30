---
title: 創建和使用資源檔 - Azure 批次處理
description: 瞭解如何從各種輸入源創建批次處理資源檔。 本文介紹了有關如何在 VM 上創建和放置它們的幾個常見方法。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 03/18/2020
ms.author: labrenne
ms.openlocfilehash: 0fe859ac30e7b8050d1f4688d7cf106a465e7566
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531136"
---
# <a name="creating-and-using-resource-files"></a>創建和使用資源檔

Azure 批次處理任務通常需要某種形式的資料來處理。 資源檔是通過任務向 Batch 虛擬機器 （VM） 提供此資料的方法。 所有類型的任務都支援資源檔：任務、啟動任務、作業準備任務、作業發佈任務等。本文介紹了一些有關如何創建資源檔並將其放置在 VM 上的常用方法。  

資源檔將資料放入批次處理中的 VM，但資料類型及其使用方式是靈活的。 但是，有一些常見的用例：

1. 使用啟動任務上的資源檔在每個 VM 上預配公共檔
1. 預配要由任務處理的輸入資料

例如，常見檔可以是用於安裝任務運行的應用程式的啟動任務上的檔。 輸入資料可以是原始圖像或視頻資料，也可以是批次處理要處理的任何資訊。

## <a name="types-of-resource-files"></a>資源檔的類型

有幾個不同的選項可用於生成資源檔。 資源檔的創建過程因原始資料的存儲位置而異。

用於創建資源檔的選項：

- [存儲容器 URL](#storage-container-url)：從 Azure 中的任何存儲容器生成資源檔
- [存儲容器名稱](#storage-container-name)：從連結到 Batch 的 Azure 存儲帳戶中的容器名稱生成資源檔
- [Web 終結點](#web-endpoint)：從任何有效的 HTTP URL 生成資源檔

### <a name="storage-container-url"></a>存儲容器 URL

使用存儲容器 URL 意味著，具有正確的許可權，可以訪問 Azure 中的任何存儲容器中的檔。 

在此 C# 示例中，檔已上載到 Azure 存儲容器作為 blob 存儲。 要訪問創建資源檔所需的資料，我們首先需要訪問存儲容器。

創建具有訪問存儲容器的正確許可權的共用訪問簽名 （SAS） URI。 設置 SAS 的過期時間和許可權。 在這種情況下，不指定啟動時間，因此 SAS 將立即生效，並在生成後兩小時過期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 對於容器訪問，您必須同時`Read`具有和`List`許可權，而使用 Blob 訪問時，只需要`Read`許可權。

配置許可權後，創建 SAS 權杖並格式化 SAS URL 以訪問存儲容器。 使用存儲容器的格式化 SAS URL，使用 生成資源[`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)檔。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

生成 SAS URL 的替代方法是在 Azure Blob 存儲中啟用對容器及其 Blob 的匿名公共讀取存取。 這樣，您可以授予對這些資源的唯讀存取權限，而無需共用帳戶金鑰，並且不需要 SAS。 公共讀取存取通常用於您希望某些 Blob 始終可用於匿名讀取存取的情況。 如果此方案適合您的解決方案，請參閱["匿名存取 Blob"](../storage/blobs/storage-manage-access-to-resources.md)一文，瞭解有關管理對 Blob 資料的訪問的更多資訊。

### <a name="storage-container-name"></a>存儲容器名稱

您可以使用 Azure 存儲容器的名稱來訪問 Blob 資料，而不是配置和創建 SAS URL。 您使用的存儲容器必須位於連結到 Batch 帳戶的 Azure 存儲帳戶中。 該存儲帳戶稱為自動存儲帳戶。 使用自動存儲容器可以繞過配置和創建 SAS URL 來訪問存儲容器。

在此示例中，我們假定用於創建資源檔的資料已位於連結到 Batch 帳戶的 Azure 存儲帳戶中。 如果您沒有自動存儲帳戶，請參閱[創建 Batch 帳戶](batch-account-create-portal.md)中的步驟，瞭解如何創建和連結帳戶。

通過使用連結的存儲帳戶，您無需創建 SAS URL 並將其配置為存儲容器。 而是提供連結存儲帳戶中的存儲容器的名稱。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 終結點

未上載到 Azure 存儲的資料仍可用於創建資源檔。 您可以指定任何包含輸入資料的有效 HTTP URL。 URL 提供給批次處理 API，然後資料用於創建資源檔。

在下面的 C# 示例中，輸入資料託管在虛構的 GitHub 終結點上。 API 從有效的 Web 終結點檢索該檔，並生成任務需要使用的資源檔。 此方案不需要憑據。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>祕訣與建議

每個 Azure 批次處理任務使用不同的檔，這就是為什麼 Batch 具有可用於管理工作檔的選項。 以下方案並非全面，而是涵蓋一些常見情況並提供建議。

### <a name="many-resource-files"></a>許多資源檔

Batch 作業可能包含多個任務，這些任務都使用相同的常見檔。 如果公用任務檔在許多工之間共用，則使用應用程式包來包含檔而不是使用資源檔可能是一個更好的選擇。 應用程式包為下載速度提供優化。 此外，應用程式包中的資料在任務之間緩存，因此，如果任務檔不經常更改，應用程式包可能非常適合您的解決方案。 使用應用程式包時，無需手動管理多個資源檔或生成 SAS URL 來訪問 Azure 存儲中的檔。 批次處理在後臺使用 Azure 存儲來存儲應用程式包並將其部署到計算節點。

如果每個任務都有許多特定于該任務的檔，則資源檔是最佳選項，因為使用唯一檔的任務通常需要更新或替換，這與應用程式包內容不太容易。 資源檔為更新、添加或編輯單個檔提供了額外的靈活性。

### <a name="number-of-resource-files-per-task"></a>每個任務的資源檔數

如果任務上指定了幾百個資源檔，Batch 可能會拒絕該任務過大。 最好通過最小化任務本身上的資源檔數量來保持任務小。

如果無法最大限度地減少任務所需的檔數量，則可以通過創建引用資源檔的存儲容器的單個資源檔來優化任務。 為此，請將資源檔放入 Azure 存儲容器，並為資源檔使用不同的"容器"[方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods)。 使用 blob 首碼選項指定要為任務下載的檔集合。

## <a name="next-steps"></a>後續步驟

- 瞭解[應用程式包](batch-application-packages.md)作為資源檔的替代方法。
- 有關將容器用於資源檔的詳細資訊，請參閱[容器工作負載](batch-docker-container-workloads.md)。
- 要瞭解如何從任務中收集和保存輸出資料，請參閱["持久作業和任務輸出](batch-task-output.md)"。
- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
