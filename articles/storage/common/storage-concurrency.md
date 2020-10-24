---
title: 管理並行存取
titleSuffix: Azure Storage
description: 瞭解如何在 Azure 儲存體中管理 Blob、佇列、資料表和檔案服務的平行存取。 瞭解所使用的三種主要資料並行策略。
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: ac54282135759f14f17ed16b9779013f849bd8d7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488668"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>管理 Microsoft Azure 儲存體中的並行存取

以網際網路為基礎的現代應用程式，通常會有多個使用者同時檢視及更新資料。 應用程式開發人員必須仔細考慮如何提供可預測的使用者體驗，特別是當多個使用者可以更新相同的資料時。 開發人員通常會考量三個主要的資料並行存取策略：

1. 開放式平行存取–執行更新的應用程式將會在其更新時，確認資料在應用程式上次讀取該資料之後是否已變更。 例如，如果兩位使用者看到 wiki 頁面更新相同的頁面，則 wiki 平臺必須確保第二個更新不會覆寫第一個更新，而且這兩個使用者都瞭解更新是否成功。 此策略最常用在 Web 應用程式中。
2. 封閉式平行存取–想要進行更新的應用程式將會鎖定物件，防止其他使用者更新資料，直到釋放鎖定為止。
3. 最後一個寫入器獲勝–一種方法，可在應用程式第一次讀取資料之後，繼續執行任何更新作業，而不需要確認是否有任何其他應用程式更新資料。 這項策略通常會在資料分割時使用，因此不會有多個使用者存取相同資料的可能性。 在處理暫時性資料串流的情況中，也可以利用此策略。

本文概述如何藉由支援上述三種並行策略，來簡化開發的 Azure 儲存體。

## <a name="azure-storage-simplifies-cloud-development"></a>Azure 儲存體簡化雲端開發

Azure 儲存體支援這三個策略，雖然它有其功能可提供開放式和封閉式平行存取的完整支援。 Azure 儲存體是設計來採用強式一致性模型，保證當它認可資料插入或更新作業時，對該資料的進一步存取將會看到最新的更新。 使用最終一致性模型的儲存平臺，會有一個使用者執行寫入的時間與其他使用者可以看到的更新資料之間的延遲。

開發人員也應該留意儲存體平臺隔離變更的方式，尤其是跨交易對相同物件進行的變更。 Azure 儲存體服務會使用快照集隔離，以允許在單一資料分割內同時進行讀取作業和寫入作業。 不同於其他隔離層級，快照隔離可確保所有讀取皆可看見資料的一致快照，即使在更新執行時亦然 – 基本上是藉由在更新交易進行處理時傳回最新的認可值。

## <a name="managing-concurrency-in-blob-storage"></a>管理 Blob 儲存體中的並行

您可以選擇使用開放式或封閉式並行存取模型，以管理如何存取 Blob 服務中的 blob 和容器。 如果您未明確指定策略，則預設值為 [最後寫入 wins]。

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Blob 和容器的開放式並行存取

儲存體服務會為每個儲存的物件指派識別碼。 每次對物件執行更新作業時，就會更新此識別碼。 此識別碼會使用在 HTTP 通訊協定內定義的 ETag (實體標記) 標頭，隨附在 HTTP GET 回應中傳回至用戶端。 對這類物件執行更新的使用者可連同條件式標頭一起傳送，以確保只有在符合特定條件時才會進行更新。在此案例中，條件是 "If-match" 標頭，這需要儲存體服務確保更新要求中指定的 ETag 值與儲存在儲存體服務中的值相同。

此程序大致如下：

1. 從儲存體服務中擷取 Blob，回應中會包含 HTTP ETag 標頭值，用來識別儲存體服務中目前的物件版本。
2. 當您更新 Blob 時，請將您在步驟 1 中接收到的 ETag 值納入您傳送至服務之要求的 **If-Match** 條件式標頭中。
3. 服務會比較要求中的 ETag 值與 Blob 目前的 ETag 值。
4. 如果 Blob 目前的 ETag 值與要求中的 **If-Match** 條件式標頭內的 ETag 不是相同版本，服務會將 412 錯誤傳回至用戶端。 此錯誤表示用戶端自用戶端抓取之後，另一個進程已更新了 blob。
5. 如果 Blob 目前的 ETag 值與要求中的 **If-Match** 條件式標頭內的 ETag 是相同版本，服務將會執行要求的作業，並更新 Blob 目前的 ETag 值，以顯示它已建立新版本。

下列 C# 程式碼片段 (使用用戶端儲存體程式庫 4.2.0) 所顯示的簡易範例，說明如何根據從先前擷取或插入之 Blob 的屬性中存取的 ETag 值，來建構 **If-Match AccessCondition** 。 接著它會在更新 blob 時使用 **AccessCondition** 物件：**AccessCondition** 物件會將 **If-Match** 標頭新增至要求。 如果有其他程序已更新 blob，Blob 服務將會傳回 HTTP 412 (先決條件失敗) 狀態訊息。 您可以在此處下載完整的範例：[使用 Azure 儲存體管理並行存取](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}
```

Azure 儲存體也支援條件式標頭，例如，如 **有修改**，則 **為，如果**未修改，則為，如果是，則為，如果是， **則**表示這些標頭的組合。 如需詳細資訊，請參閱[指定 Blob 服務作業的條件式標頭](https://msdn.microsoft.com/library/azure/dd179371.aspx)。

下表彙總了在要求中接受條件式標頭 (例如 **If-Match** ) 以及在回應中傳回 ETag 值的容器作業。

| 作業 | 傳回容器 ETag 值 | 接受條件式標頭 |
|:--- |:--- |:--- |
| 建立容器 |是 |否 |
| 取得容器屬性 |是 |否 |
| 取得容器中繼資料 |是 |否 |
| 設定容器中繼資料 |是 |是 |
| 取得容器 ACL |是 |否 |
| 設定容器 ACL |是 |是 (*) |
| 刪除容器 |否 |是 |
| 租用容器 |是 |是 |
| 列出 Blob |否 |否 |

 ( * ) 快取 SetContainerACL 所定義的許可權，而且這些許可權的更新需要30秒的時間，在這段期間內更新不保證會保持一致。

下表彙總了在要求中接受條件式標頭 (例如 **If-Match** ) 以及在回應中傳回 ETag 值的 Blob 作業。

| 作業 | 傳回 ETag 值 | 接受條件式標頭 |
|:--- |:--- |:--- |
| 放置 Blob |是 |是 |
| 取得 Blob |是 |是 |
| 取得 Blob 屬性 |是 |是 |
| 設定 Blob 屬性 |是 |是 |
| 取得 Blob 中繼資料 |是 |是 |
| 設定 Blob 中繼資料 |是 |是 |
| 租用 Blob (*) |是 |是 |
| 快照 Blob |是 |是 |
| 複製 Blob |是 |是 (針對來源及目的地 Blob) |
| 中止複製 Blob |否 |否 |
| 刪除 Blob |否 |是 |
| 放置區塊 |否 |否 |
| 放置區塊清單 |是 |是 |
| 取得區塊清單 |是 |否 |
| 放置頁面 |是 |是 |
| 取得頁面範圍 |是 |是 |

 ( * ) 租用 Blob 不會變更 Blob 上的 ETag。

### <a name="pessimistic-concurrency-for-blobs"></a>Blob 的封閉式並行存取

若要鎖定 blob 以供獨佔使用，請取得其 [租用](https://msdn.microsoft.com/library/azure/ee691972.aspx) 。 當您取得租用時，您會指定租用的時間週期。 時間週期的範圍是從15到60秒或無限期，而其數量為獨佔鎖定。 更新有限的租用以進行擴充。 當您完成時，請釋放租用。 Blob 儲存體會在有限租用到期時自動加以釋放。

租用可以支援不同的同步處理策略。 策略包括 *獨佔寫入/共用讀取*、 *獨佔寫入/獨佔讀取*和 *共用寫入/獨佔*讀取。 如果租用存在，Azure 儲存體會強制執行獨佔寫入， (put、set 和 delete 作業) 不過，若要確保讀取作業的獨佔性需要開發人員確保所有用戶端應用程式都使用租用識別碼，且一次只有一個用戶端具有有效的租用識別碼。 未包含租用識別碼的讀取作業會導致共用讀取。

下列 C# 程式碼片段說明對某個 Blob 取得獨佔租用 30 秒、更新該 Blob 的內容，然後釋放租用的範例。 當您嘗試取得新的租用時，如果 blob 上已有有效的租用，Blob 服務會傳回「HTTP (409) 衝突」狀態結果。 下列程式碼片段在要求更新儲存體服務中的 Blob 時，使用 **AccessCondition** 物件封裝租用資訊。  您可以在此處下載完整的範例：[使用 Azure 儲存體管理並行存取](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}
```

如果您直接嘗試對已租用的 Blob 執行寫入作業，而未傳送租用識別碼，要求將會失敗，並出現 412 錯誤。 如果租用在呼叫 **UploadText** 方法之前過期，但您仍然傳遞租用識別碼，要求也會失敗，並出現 **412** 錯誤。 如需管理租用到期時間和租用識別碼的詳細資訊，請參閱 [租用 Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx) REST 文件。

下列 Blob 作業可使用租用來管理封閉式並行存取：

* 放置 Blob
* 取得 Blob
* 取得 Blob 屬性
* 設定 Blob 屬性
* 取得 Blob 中繼資料
* 設定 Blob 中繼資料
* 刪除 Blob
* 放置區塊
* 放置區塊清單
* 取得區塊清單
* 放置頁面
* 取得頁面範圍
* 快照 Blob - 若有租用存在，租用識別碼則是選用的
* 複製 Blob - 若有租用存在於目的地 Blob 上，則必須要有租用識別碼
* 中止複製 Blob - 若有無限制租用存在於目的地 Blob 上，則必須要有租用識別碼
* 租用 Blob

### <a name="pessimistic-concurrency-for-containers"></a>容器的封閉式並行存取

容器上的租用會啟用相同的同步處理策略， (*獨佔寫入/共用讀取*、 *獨佔寫入/獨佔讀取*和 *共用寫入/獨佔讀取*) ，但不同于 blob，儲存體服務只會強制執行刪除作業的獨佔性。 若要刪除具有作用中租用的容器，用戶端必須使用刪除要求納入作用中的租用識別碼。 其他所有的容器作業皆可在已租用的容器上成功執行，而無須納入租用識別碼；在此情況下，這些作業屬於共用作業。 如果更新 (放置或設定) 或讀取作業需要獨佔性，則開發人員應確定所有的用戶端都使用同一個租用識別碼，並確定同一時間只有一個用戶端具有有效的租用識別碼。

下列容器作業可使用租用來管理封閉式並行存取：

* 刪除容器
* 取得容器屬性
* 取得容器中繼資料
* 設定容器中繼資料
* 取得容器 ACL
* 設定容器 ACL
* 租用容器

如需詳細資訊，請參閱

* [指定 Blob 服務作業的條件式標頭](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [租用容器](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [租用 Blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>管理表格儲存體中的並行

當您使用實體時，表格服務會使用開放式平行存取檢查做為預設行為，而不像您必須明確選擇進行開放式平行存取檢查的 Blob 服務。 資料表和 Blob 服務的另一個差異在於您只能管理實體的並行行為，但使用 Blob 服務時，您可以管理容器和 blob 的並行。

若要使用開放式並行存取，並檢查在您從資料表儲存體服務中擷取實體後是否有其他程序修改了該實體，您可以使用您在資料表服務傳回實體時所接收的 ETag 值。 此程序大致如下：

1. 從資料表儲存體服務中擷取實體，回應中會包含 ETag 值，用來識別與儲存體服務中的該實體相關聯的現行識別碼。
2. 當您更新實體時，請將您在步驟 1 中接收到的 ETag 值納入您傳送至服務之要求的必要 **If-Match** 標頭中。
3. 服務會比較要求中的 ETag 值與實體目前的 ETag 值。
4. 如果實體目前的 ETag 值與要求中的必要 **If-Match** 標頭內的 ETag 不同，服務會將 412 錯誤傳回至用戶端。 這會向用戶端指出在用戶端擷取實體後，有另一個程序更新過該實體。
5. 如果實體目前的 ETag 值與要求中的強制 **if-match** 標頭中的 etag 相同，或 **if-match** 標頭中包含萬用字元 ( * ) ，則服務會執行要求的作業，並更新實體目前的 etag 值，以顯示它已更新。

表格服務要求用戶端必須在更新要求中包含 **if-match** 標頭。 但如果用戶端將要求中的 **If-Match** 標頭設為萬用字元 (*)，則有可能強制執行非條件式更新 (「最後寫入為準」策略)，並略過並行存取檢查。

下列 C# 程式碼片段說明先前建立或擷取的客戶實體更新了其電子郵件地址。 初始插入或擷取作業將 ETag 值儲存在客戶物件中，且因為範例在執行取代作業時使用了相同的物件執行個體，因此自動將 ETag 值傳回至資料表服務，使服務能夠檢查是否有並行存取違規。 如果有其他程序更新了資料表儲存體中的實體，服務將會傳回 HTTP 412 (預先指定的條件失敗) 狀態訊息。  您可以在此處下載完整的範例：[使用 Azure 儲存體管理並行存取](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)。

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}
```

若要明確停用並行存取檢查，您應在執行取代作業之前，將 **employee** 物件的 **ETag** 屬性設為 "*"。

```csharp
customer.ETag = "*";
```

下表彙總了資料表實體作業使用 ETag 值的情形：

| 作業 | 傳回 ETag 值 | 需要 If-Match 要求標頭 |
|:--- |:--- |:--- |
| 查詢實體 |是 |否 |
| 插入實體 |是 |否 |
| 更新實體 |是 |是 |
| 合併實體 |是 |是 |
| 刪除實體 |否 |是 |
| 插入或取代實體 |是 |否 |
| 插入或合併實體 |是 |否 |

請注意， **插入或取代實體** 和 **插入或合併實體** 作業 *不* 會進行任何平行存取檢查，因為它們不會將 ETag 值傳送至表格服務。

一般情況下，使用資料表的開發人員應該依賴開放式平行存取。 如果您在存取資料表時需要封閉式鎖定，請為每個資料表指派一個選擇的 blob，並在運算元據表之前嘗試在 blob 上取得租用。 這種方法需要應用程式確保所有資料存取路徑在運算元據表之前都取得租用。 您也應該注意到，最小租用時間是15秒，需要謹慎考慮調整規模。

如需詳細資訊，請參閱

* [實體上的作業](https://msdn.microsoft.com/library/azure/dd179375.aspx)

## <a name="managing-concurrency-in-the-queue-service"></a>管理佇列服務中的並行存取

在使用佇列服務時必須考量並行存取的案例之一，是有多個用戶端從一個佇列擷取訊息時。 從佇列擷取訊息時，回應中會包含訊息，以及刪除訊息所需的 pop receipt 值。 訊息不會自動從佇列中刪除，但在抓取之後，其他用戶端看不到可見度 timeout 參數所指定時間間隔內的訊息。 抓取訊息的用戶端應該在處理訊息之後，以及回應的 TimeNextVisible 元素所指定的時間之前刪除訊息，這是根據可見度 timeout 參數的值來計算。 可見度 timeout 的值會加入至訊息的取出時間，以判斷 TimeNextVisible 的值。

佇列服務不支援開放式或封閉式平行存取，因此，處理從佇列抓取之訊息的用戶端，應該確保以等冪的方式處理訊息。 最後一個寫入器獲勝策略用於更新作業，例如 SetQueueServiceProperties、SetQueueMetaData、SetQueueACL 和 UpdateMessage。

如需詳細資訊，請參閱

* [佇列服務 REST API](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [取得訊息](https://msdn.microsoft.com/library/azure/dd179474.aspx)

## <a name="managing-concurrency-in-azure-files"></a>管理 Azure 檔案儲存體中的並行

檔案服務可使用兩種不同的通訊協定端點來存取 – SMB 和 REST。 REST 服務不支援開放式鎖定或封閉式鎖定，而且所有更新都會遵循最後寫入者獲勝策略。 掛接檔案共用的 SMB 用戶端可以使用檔案系統鎖定機制來管理共用檔案的存取權，包括執行封閉式鎖定的能力。 SMB 用戶端在開啟檔案時，會同時指定檔案存取和共用模式。 將 [檔案存取] 選項設為 [寫入] 或 [讀取/寫入]，並將 [檔案共用] 模式設為 [無]，會使檔案被 SMB 用戶端鎖定，直到檔案關閉為止。 如果嘗試對已由 SMB 用戶端鎖定的檔案執行 REST 作業，REST 服務將會傳回狀態碼 409 (衝突) 和錯誤碼 SharingViolation。

SMB 用戶端在開啟檔案以進行刪除時，會將檔案標示為「擱置刪除」，直到所有對該檔案的其他 SMB 用戶端開啟控制代碼關閉為止。 在檔案標示為「擱置刪除」時，任何對該檔案的 REST 作業都將傳回狀態碼 409 (衝突) 和錯誤碼 SMBDeletePending。 找不到狀態碼 404 (找不到) 因為 SMB 用戶端可能會在關閉檔案之前移除暫止刪除旗標，所以不會傳回。 換句話說，狀態碼 404 (找不到) 只有可能在檔案已移除時出現。 請注意，處於 SMB 擱置刪除狀態的檔案不會包含在「列出檔案」結果中。 另請注意，「REST 刪除檔案」和「REST 刪除目錄」作業會以不可分割的方式認可，因此不會導致擱置刪除狀態。

如需詳細資訊，請參閱

* [管理檔案鎖定](https://msdn.microsoft.com/library/azure/dn194265.aspx)

## <a name="next-steps"></a>後續步驟

如需此部落格中參考的完整範例應用程式：

* [使用 Azure 儲存體管理並行存取 - 範例應用程式](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)

如需 Azure 儲存體的詳細資訊，請參閱：

* [Microsoft Azure 儲存體首頁](https://azure.microsoft.com/services/storage/)
* [Azure 儲存體簡介](storage-introduction.md)
* [Blob](../blobs/storage-dotnet-how-to-use-blobs.md)、[資料表](../../cosmos-db/table-storage-how-to-use-dotnet.md)、[佇列](../storage-dotnet-how-to-use-queues.md)及[檔案](../storage-dotnet-how-to-use-files.md)的儲存體入門
* 儲存體架構 – [Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)
