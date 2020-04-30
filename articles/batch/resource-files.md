---
title: 建立和使用資源檔
description: 瞭解如何從各種輸入來源建立 Batch 資源檔。 本文涵蓋一些常見的方法，說明如何建立並將它們放置在 VM 上。
ms.date: 03/18/2020
ms.topic: article
ms.openlocfilehash: c9a2e581d0cada467e89e3da731fac7f78b22992
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "82117177"
---
# <a name="creating-and-using-resource-files"></a>建立和使用資源檔

Azure Batch 工作通常需要處理某種形式的資料。 資源檔是透過工作將這項資料提供給 Batch 虛擬機器（VM）的方式。 所有類型的工作都支援資源檔：工作、啟動工作、作業準備工作、作業釋放工作等等。本文涵蓋一些常見的方法，說明如何建立資源檔，並將它們放在 VM 上。  

資源檔會以批次方式將資料放入 VM，但資料的類型和使用方式是彈性的。 不過，有一些常見的使用案例：

1. 在啟動工作上使用資源檔在每個 VM 上布建通用檔案
1. 布建要由工作處理的輸入資料

例如，一般檔案可能是啟動工作上的檔案，用來安裝您的工作執行的應用程式。 輸入資料可能是未經處理的影像或影片資料，或任何要由批次處理的資訊。

## <a name="types-of-resource-files"></a>資源檔案類型

有幾個不同的選項可用來產生資源檔。 資源檔的建立程式會根據原始資料的儲存位置而有所不同。

建立資源檔的選項：

- [儲存體容器 URL](#storage-container-url)：從 Azure 中的任何儲存體容器產生資源檔
- [儲存體容器名稱](#storage-container-name)：從連結到 Batch 的 Azure 儲存體帳戶中的容器名稱產生資源檔
- [Web 端點](#web-endpoint)：從任何有效的 HTTP URL 產生資源檔

### <a name="storage-container-url"></a>儲存體容器 URL

使用儲存體容器 URL 表示，透過正確的許可權，您可以存取 Azure 中任何儲存體容器中的檔案。 

在此 c # 範例中，檔案已上傳至 Azure 儲存體容器做為 blob 儲存體。 若要存取建立資源檔所需的資料，我們必須先取得儲存體容器的存取權。

建立具有正確許可權的共用存取簽章（SAS） URI 來存取儲存體容器。 設定 SAS 的到期時間和許可權。 在此情況下，系統不會指定開始時間，因此 SAS 會立即生效，並在產生後的兩小時後到期。

```csharp
SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
{
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
    Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.List
};
```

> [!NOTE]
> 針對容器存取，您必須同時`Read`擁有和`List`許可權，而使用 blob 存取時，您只`Read`需要許可權。

設定許可權之後，請建立 SAS 權杖並格式化 SAS URL，以存取儲存體容器。 針對儲存體容器使用格式化的 SAS URL，以[`FromStorageContainerUrl`](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile.fromstoragecontainerurl?view=azure-dotnet)產生資源檔。

```csharp
CloudBlobContainer container = blobClient.GetContainerReference(containerName);

string sasToken = container.GetSharedAccessSignature(sasConstraints);
string containerSasUrl = String.Format("{0}{1}", container.Uri, sasToken);

ResourceFile inputFile = ResourceFile.FromStorageContainerUrl(containerSasUrl);
```

產生 SAS URL 的替代方法是在 Azure Blob 儲存體中啟用對容器及其 blob 的匿名、公用讀取權限。 如此一來，您就可以授與這些資源的唯讀存取權，而不需共用您的帳戶金鑰，也不需要 SAS。 公用讀取權限通常用於您想要某些 blob 永遠可供匿名讀取存取的情況。 如果此案例適合您的解決方案，請參閱[匿名存取 blob](../storage/blobs/storage-manage-access-to-resources.md)一文，以深入瞭解如何管理 blob 資料的存取權。

### <a name="storage-container-name"></a>儲存體容器名稱

您可以使用 Azure 儲存體容器的名稱來存取 blob 資料，而不需要設定和建立 SAS URL。 您所使用的儲存體容器必須位於連結至 Batch 帳戶的 Azure 儲存體帳戶中。 該儲存體帳戶稱為 autostorage-keys 帳戶。 使用 autostorage-keys 容器可讓您略過設定和建立 SAS URL，以存取儲存體容器。

在此範例中，我們假設要用於建立資源檔的資料已在連結至您 Batch 帳戶的 Azure 儲存體帳戶中。 如果您沒有 autostorage-keys 帳戶，請參閱[建立 Batch 帳戶](batch-account-create-portal.md)中的步驟，以取得如何建立和連結帳戶的詳細資料。

藉由使用連結的儲存體帳戶，您不需要建立和設定儲存體容器的 SAS URL。 相反地，請在您連結的儲存體帳戶中提供儲存體容器的名稱。

```csharp
ResourceFile inputFile = ResourceFile.FromAutoStorageContainer(containerName);
```

### <a name="web-endpoint"></a>Web 端點

尚未上傳至 Azure 儲存體的資料仍然可以用來建立資源檔。 您可以指定包含輸入資料的任何有效 HTTP URL。 URL 會提供給 Batch API，然後使用該資料來建立資源檔。

在下列 c # 範例中，輸入資料裝載于虛構的 GitHub 端點。 API 會從有效的 web 端點抓取檔案，並產生您的工作所要使用的資源檔。 此案例不需要任何認證。

```csharp
ResourceFile inputFile = ResourceFile.FromUrl("https://github.com/foo/file.txt", filePath);
```

## <a name="tips-and-suggestions"></a>祕訣與建議

每個 Azure Batch 工作都會以不同的方式使用檔案，這也是為什麼 Batch 有選項可用來管理工作上的檔案。 下列案例並不是完整的，而是涵蓋一些常見的情況並提供建議。

### <a name="many-resource-files"></a>許多資源檔

您的批次作業可能包含數個全都使用相同通用檔案的工作。 如果在許多工作中共用一般工作檔案，使用應用程式封裝來包含檔案，而不是使用資源檔，則可能是較好的選項。 應用程式封裝提供下載速度的優化。 此外，在工作之間快取應用程式封裝中的資料，因此，如果您的工作檔案不常變更，則應用程式套件可能適合您的方案。 使用應用程式套件時，您不需要手動管理數個資源檔，或產生 SAS Url 來存取 Azure 儲存體中的檔案。 Batch 會在背景中運作，並 Azure 儲存體來儲存應用程式套件，並將其部署至計算節點。

如果每個工作都有許多該工作特有的檔案，資源檔是最佳選項，因為使用唯一檔案的工作通常需要更新或取代，這與應用程式封裝內容並不容易。 資源檔可為更新、新增或編輯個別檔案提供額外的彈性。

### <a name="number-of-resource-files-per-task"></a>每項工作的資源檔數目

如果在工作上指定了數百個資源檔，Batch 可能會拒絕工作太大。 最好是將工作本身的資源檔數目減至最少，讓您的工作變小。

如果沒有任何方法可將您的工作所需的檔案數目降至最低，您可以建立單一資源檔來參考資源檔的儲存體容器，以將工作優化。 若要這麼做，請將您的資源檔放入 Azure 儲存體容器中，並對資源檔使用不同的 "container"[方法](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.resourcefile?view=azure-dotnet#methods)。 使用 blob 前置詞選項來指定要為您的工作下載的檔案集合。

## <a name="next-steps"></a>後續步驟

- 瞭解[應用程式套件](batch-application-packages.md)做為資源檔的替代方案。
- 如需使用資源檔容器的詳細資訊，請參閱[容器工作負載](batch-docker-container-workloads.md)。
- 若要瞭解如何從您的工作收集並儲存輸出資料，請參閱[保存作業和工作輸出](batch-task-output.md)。
- 了解可用來建置 Batch 解決方案的 [Batch API 和工具](batch-apis-tools.md)。
