---
title: 建立和使用資源檔
description: 了解如何從各種輸入來源建立 Batch 資源檔。 此文章涵蓋一些常見的方法，說明如何建立並將其放置在 VM 上。
ms.date: 03/18/2020
ms.topic: how-to
ms.openlocfilehash: 22c0220d08660402bef3fd4aaf0add6adc12a295
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230937"
---
# <a name="creating-and-using-resource-files"></a>建立和使用資源檔

Azure Batch 工作通常需要處理特定形式的資料。 資源檔是透過工作將此資料提供給 Batch 虛擬機器 (VM) 的方式。 所有類型的工作都支援資源檔：工作、啟動工作、作業準備工作、作業解除工作等等。此文章涵蓋一些常見的方法，說明如何建立資源檔並將其放置在 VM 上。  

資源檔會將資料放入 Batch 中的 VM，但資料的類型和使用方式是彈性的。 但是，有一些常見的使用案例：

1. 在每個 VM 上使用啟動工作的資源檔佈建通用檔案
1. 佈建要由工作處理的輸入資料

例如，通用檔案可能是啟動工作的檔案，用來安裝您工作執行的應用程式。 輸入資料可能是未經處理的影像或影片資料，或要由 Batch 處理的任何資訊。

## <a name="types-of-resource-files"></a>資源檔類型

有幾個不同的選項可用來產生資源檔。 資源檔的建立程序會根據原始資料的儲存位置而有所不同。

用於建立資源檔的選項：

- [儲存體容器 URL](#storage-container-url)：從 Azure 中的任何儲存體容器產生資源檔
- [儲存體容器名稱](#storage-container-name)：在連結到 Batch 的 Azure 儲存體帳戶中，從容器名稱產生資源檔
- [Web 端點](#web-endpoint)：從任何有效的 HTTP URL 產生資源檔

### <a name="storage-container-url"></a>儲存體容器 URL

使用儲存體容器 URL 意味著，利用正確的權限，您可以存取 Azure 中任何儲存體容器內的檔案。 

在此 C# 範例中，檔案已上傳至 Azure 儲存體容器，作為 Blob 儲存體。 若要存取建立資源檔所需的資料，我們必須先取得儲存體容器的存取權。

利用正確的權限建立共用存取簽章 (SAS) URI 以存取儲存體容器。 設定 SAS 的到期時間和權限。 在此情況下，系統不會指定開始時間，因此 SAS 會立即生效，並在產生後的兩小時後到期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 若要存取容器，您必須同時具備 `Read` 和 `List` 權限；若要存取 Blob，您只需要 `Read` 權限。

設定權限之後，建立 SAS 權杖並設定 SAS URL 格式，以存取儲存體容器。 針對儲存體容器使用格式化的 SAS URL 可產生具有 [`FromStorageContainerUrl`](/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl) 的資源檔。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

產生 SAS URL 的替代方式是，針對 Azure Blob 儲存體中的容器及其 Blob 啟用匿名與公用讀取權限。 如此您就可以將這些資源的唯讀存取權限授與他人，而無須共用您的帳戶金鑰，也無須使用 SAS。 公用讀取權限通常用於您想要某些 Blob 永遠可供匿名讀取存取的狀況。 如果此案例適合您的解決方案，請參閱[匿名存取 Blob](../storage/blobs/storage-manage-access-to-resources.md) 一文，以深入了解如何管理 Blob 資料的存取權。

### <a name="storage-container-name"></a>儲存體容器名稱

您可以使用 Azure 儲存體容器的名稱來存取 Blob 資料，而不需要設定和建立 SAS URL。 您所使用的儲存體容器必須位於連結至 Batch 帳戶的 Azure 儲存體帳戶中。 該儲存體帳戶稱為 autostorage 帳戶。 使用 autostorage 容器可讓您略過設定和建立 SAS URL，就可以存取儲存體容器。

在此範例中，我們假設要用於建立資源檔的資料已在連結至您 Batch 帳戶的 Azure 儲存體帳戶中。 如果您沒有 autostorage 帳戶，請參閱[建立 Batch 帳戶](batch-account-create-portal.md)中的步驟，以取得如何建立和連結帳戶的詳細資料。

藉由使用連結的儲存體帳戶，您就不需要建立和設定儲存體容器的 SAS URL。 但是，請在您連結的儲存體帳戶中提供儲存體容器的名稱。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 端點

未上傳至 Azure 儲存體的資料仍然可以用來建立資源檔。 您可以指定包含輸入資料的任何有效 HTTP URL。 系統會將 URL 提供給 Batch API，然後使用該資料建立資源檔。

在下列 C# 範例中，輸入資料裝載在虛構的 GitHub 端點上。 API 會從有效的 Web 端點擷取檔案，並產生您工作所要取用的資源檔。 此案例不需要任何認證。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>祕訣與建議

每個 Azure Batch 工作都會以不同的方式使用檔案，這也是為什麼 Batch 有選項可用來管理工作上的檔案。 下列案例並不是完整的，但是涵蓋了一些常見的情況並提供建議。

### <a name="many-resource-files"></a>許多資源檔

您的 Batch 作業可能包含全都使用相同通用檔案的數個工作。 如果在許多工作中共用通用工作檔案，使用應用程式套件包含檔案，而不是使用資源檔，可能是更好的選項。 應用程式套件可將下載速度最佳化。 此外，系統會在工作之間快取應用程式套件中的資料，因此，如果您的工作檔案不常變更，則應用程式套件可能適合您的解決方案。 使用應用程式套件時，您不需要手動管理數個資源檔或產生 SAS URL，就可以存取 Azure 儲存體中的檔案。 Batch 會在背景搭配 Azure 儲存體運作，以儲存應用程式套件，並將其部署到計算節點。

如果每個工作都有該工作專屬的許多檔案，則資源檔是最佳選項，因為使用唯一檔案的工作通常需要更新或取代，這與應用程式套件內容並不容易。 資源檔可為更新、新增或編輯個別檔案提供額外的彈性。

### <a name="number-of-resource-files-per-task"></a>每項工作的資源檔數目

如果針對工作指定了數百個資源檔，Batch 可能會因為太大而拒絕工作。 最好是將工作本身的資源檔數目減至最少，讓您的工作變小。

如果沒有任何方法可將您工作所需的檔案數目減至最少，您可以建立能夠參考資源檔儲存體容器的單一資源檔，以便將工作最佳化。 若要這麼做，請將您的資源檔放入 Azure 儲存體容器中，並針對資源檔使用不同的「容器」[方法](/dotnet/api/microsoft.azure.batch.resourcefile#methods)。 使用 Blob 前置詞選項指定要為您的工作下載的檔案集合。

## <a name="next-steps"></a>後續步驟

- 了解[應用程式套件](batch-application-packages.md)，作為資源檔的替代方案。
- 如需有關將容器用於資源檔的詳細資訊，請參閱[容器工作負載](batch-docker-container-workloads.md)。
- 若要了解如何從您的工作收集並儲存輸出資料，請參閱[持續作業及工作輸出](batch-task-output.md)。
- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
