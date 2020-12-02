---
title: Azure Blob 儲存體作為事件方格來源
description: 描述 Azure Event Grid blob 儲存體事件的屬性
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1a81b30fcb775f5e8bc99bda70307f7f1aed9796
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452551"
---
# <a name="azure-blob-storage-as-an-event-grid-source"></a>以事件方格來源 Azure Blob 儲存體

本文提供 blob 儲存體事件的屬性與結構描述。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。 它也提供快速入門和教學課程的清單，讓您使用 Azure Blob 儲存體作為事件來源。


>[!NOTE]
> 只有 StorageV2 類型的儲存體帳戶 **(一般用途 v2)**、 **BlockBlobStorage** 和 **BlobStorage** 支援事件整合。 **儲存體 (一般用途 v1)** 不 *支援與* 事件方格整合。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="list-of-events-for-blob-rest-apis"></a>Blob REST Api 的事件清單

當用戶端透過呼叫 Blob REST Api 來建立、取代或刪除 blob 時，就會觸發這些事件。

> [!NOTE]
> `$logs`和 `$blobchangefeed` 容器不會與事件方格整合，因此這些容器中的活動不會產生事件。 此外， *`(abfss://URI) `* 針對未啟用階層命名空間的帳戶使用 dfs 端點將不會產生事件，但 blob 端點 *`(wasb:// URI)`* 將會產生事件。

 |事件名稱 |描述|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |建立或取代 blob 時觸發。 <br>具體而言，當用戶端使用 `PutBlob` `PutBlockList` `CopyBlob` 可在 Blob REST API 中使用的、或作業時，就會觸發此事件。   |
 |**Microsoft.Storage.BlobDeleted** |在刪除 blob 時觸發。 <br>具體而言，當用戶端呼叫 `DeleteBlob` Blob REST API 中可用的作業時，就會觸發此事件。 |

> [!NOTE]
> 針對 **Azure Blob 儲存體**，如果您想要確保只有在完全認可區塊 Blob 時才觸發 **BlobCreated** 事件，請篩選 `CopyBlob` 、 `PutBlob` 和 `PutBlockList` REST API 呼叫的事件。 只有在資料完全認可至區塊 Blob 之後，這些 API 呼叫才會觸發 **BlobCreated** 事件。 若要瞭解如何建立篩選，請參閱 [事件方格的篩選事件](./how-to-filter-events.md)。

### <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure Data Lake Storage Gen 2 REST Api 的事件清單

如果您在儲存體帳戶上啟用階層式命名空間，而且用戶端使用 Azure Data Lake Storage Gen2 REST Api，則會觸發這些事件。 如需 bout about Azure Data Lake Storage Gen2 的詳細資訊，請參閱 [Azure Data Lake Storage Gen2 簡介](../storage/blobs/data-lake-storage-introduction.md)。

|事件名稱|描述|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | 建立或取代 blob 時觸發。 <br>具體而言，當用戶端使用 `CreateFile` `FlushWithClose` Azure Data Lake Storage Gen2 REST API 中提供的和作業時，就會觸發此事件。 |
|**Microsoft.Storage.BlobDeleted** |在刪除 blob 時觸發。 <br>具體而言，當用戶端呼叫 Azure Data Lake Storage Gen2 REST API 所提供的作業時，也會觸發這個事件 `DeleteFile` 。 |
|**BlobRenamed**|重新命名 blob 時觸發。 <br>具體而言，當用戶端使用 Azure Data Lake Storage Gen2 REST API 所提供的作業時，就會觸發此事件 `RenameFile` 。|
|**DirectoryCreated**|建立目錄時觸發。 <br>具體而言，當用戶端使用 Azure Data Lake Storage Gen2 REST API 所提供的作業時，就會觸發此事件 `CreateDirectory` 。|
|**DirectoryRenamed**|當目錄重新命名時觸發。 <br>具體而言，當用戶端使用 Azure Data Lake Storage Gen2 REST API 所提供的作業時，就會觸發此事件 `RenameDirectory` 。|
|**DirectoryDeleted**|在刪除目錄時觸發。 <br>具體而言，當用戶端使用 Azure Data Lake Storage Gen2 REST API 所提供的作業時，就會觸發此事件 `DeleteDirectory` 。|

> [!NOTE]
> 針對 **Azure Data Lake Storage Gen2**，如果您想要確保只有在完全認可區塊 Blob 時才觸發 **BlobCreated** 事件，請篩選 `FlushWithClose` REST API 呼叫的事件。 只有在資料完全認可至區塊 Blob 之後，此 API 呼叫才會觸發 **BlobCreated** 事件。 若要瞭解如何建立篩選，請參閱 [事件方格的篩選事件](./how-to-filter-events.md)。

<a name="example-event"></a>
### <a name="the-contents-of-an-event-response"></a>事件回應的內容

觸發事件時，事件方格服務會將該事件的相關資料傳送至訂閱端點。

本章節包含每個 blob 儲存體事件的資料外觀範例。

### <a name="microsoftstorageblobcreated-event"></a>BlobCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/test-container/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 524288,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/new-file.txt",
    "sequencer": "00000000000004420000000000028963",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>BlobCreated 事件 (Data Lake Storage Gen2) 

如果 blob 儲存體帳戶具有階層命名空間，則資料看起來會像先前的範例，但這些變更例外：

* 索引 `dataVersion` 鍵設定為的值 `2` 。

* 此索引 `data.api` 鍵會設定為字串 `CreateFile` 或 `FlushWithClose` 。

* 此索引 `contentOffset` 鍵包含在資料集內。

> [!NOTE]
> 如果應用程式使用作業將 `PutBlockList` 新的 blob 上傳至帳戶，資料將不會包含這些變更。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/new-file.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "eTag": "\"0x8D4BCC2E4835CD0\"",
    "contentType": "text/plain",
    "contentLength": 0,
    "contentOffset": 0,
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/new-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event"></a>BlobDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-11-07T20:09:22.5674003Z",
  "id": "4c2359fe-001e-00ba-0e04-58586806d298",
  "data": {
    "api": "DeleteBlob",
    "requestId": "4c2359fe-001e-00ba-0e04-585868000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.blob.core.windows.net/testcontainer/file-to-delete.txt",
    "sequencer": "0000000000000281000000000002F5CA",
    "storageDiagnostics": {
      "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>BlobDeleted 事件 (Data Lake Storage Gen2) 

如果 blob 儲存體帳戶具有階層命名空間，則資料看起來會像先前的範例，但這些變更例外：

* 索引 `dataVersion` 鍵設定為的值 `2` 。

* 此索引 `data.api` 鍵會設定為字串 `DeleteFile` 。

* 索引 `url` 鍵包含路徑 `dfs.core.windows.net` 。

> [!NOTE]
> 如果應用程式使用作業 `DeleteBlob` 來刪除帳戶中的 blob，資料將不會包含這些變更。

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/file-to-delete.txt",
  "eventType": "Microsoft.Storage.BlobDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
    "api": "DeleteFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "contentType": "text/plain",
    "blobType": "BlockBlob",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/file-to-delete.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "2",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstorageblobrenamed-event"></a>BlobRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-file.txt",
  "eventType": "Microsoft.Storage.BlobRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameFile",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-file.txt",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-file.txt",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorycreated-event"></a>DirectoryCreated 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-new-directory",
  "eventType": "Microsoft.Storage.DirectoryCreated",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "CreateDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-new-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectoryrenamed-event"></a>DirectoryRenamed 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/my-renamed-directory",
  "eventType": "Microsoft.Storage.DirectoryRenamed",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "RenameDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "destinationUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-renamed-directory",
    "sourceUrl": "https://my-storage-account.dfs.core.windows.net/my-file-system/my-original-directory",
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="microsoftstoragedirectorydeleted-event"></a>DirectoryDeleted 事件

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/my-storage-account",
  "subject": "/blobServices/default/containers/my-file-system/blobs/directory-to-delete",
  "eventType": "Microsoft.Storage.DirectoryDeleted",
  "eventTime": "2017-06-26T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "api": "DeleteDirectory",
    "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
    "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
    "url": "https://my-storage-account.dfs.core.windows.net/my-file-system/directory-to-delete",
    "recursive": "true", 
    "sequencer": "00000000000004420000000000028963",  
    "storageDiagnostics": {
    "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
    }
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主旨的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| 識別碼 | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 「事件方格」會定義最上層屬性的結構描述。 「事件方格」提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| api | 字串 | 觸發事件的作業。 |
| clientRequestId | 字串 | 用戶端提供的儲存體 API 作業要求識別碼。 您可以使用此識別碼，在記錄檔中使用 "client-request-id" 欄位將 Azure 儲存體診斷記錄相互關聯，並可在用戶端要求中使用 "x------------------------- 請參閱[記錄格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字串 | 服務產生的儲存體 API 作業要求識別碼。 可用於利用記錄中的 "request-id-header" 欄位與 Azure 儲存體診斷記錄建立關聯，並從 'x-ms-request-id' 標頭中的 API 呼叫初始化傳回。 請參閱[記錄格式](/rest/api/storageservices/storage-analytics-log-format)。 |
| etag | 字串 | 您可以用來有條件地執行作業的值。 |
| ContentType | 字串 | 為 blob 指定內容類型。 |
| contentLength | integer | Blob 大小 (以位元組為單位)。 |
| blobType | 字串 | Blob 的類型。 有效值為 "BlockBlob" 或 "PageBlob"。 |
| contentOffset | 數目 | 寫入作業的位移（以位元組為單位），在事件觸發應用程式完成寫入檔案的時間點。 <br>只顯示在具有階層命名空間的 blob 儲存體帳戶上觸發的事件。|
| destinationUrl |字串 | 作業完成後將存在之檔案的 url。 例如，如果重新命名檔案， `destinationUrl` 屬性會包含新檔案名的 url。 <br>只顯示在具有階層命名空間的 blob 儲存體帳戶上觸發的事件。|
| sourceUrl |字串 | 在作業完成之前存在之檔案的 url。 例如，如果重新命名檔案，則會在 `sourceUrl` 重新命名作業之前包含原始檔案名稱的 url。 <br>只顯示在具有階層命名空間的 blob 儲存體帳戶上觸發的事件。 |
| url | 字串 | blob 的路徑。 <br>如果用戶端使用 Blob REST API，則 url 的結構如下： `<storage-account-name>.blob.core.windows.net\<container-name>\<file-name>` 。 <br>如果用戶端使用 Data Lake Storage REST API，則 url 的結構如下： `<storage-account-name>.dfs.core.windows.net/<file-system-name>/<file-name>` 。 |
| 遞迴 | 字串 | `True` 在所有子目錄上執行作業;否則為 `False` 。 <br>只顯示在具有階層命名空間的 blob 儲存體帳戶上觸發的事件。 |
| 排序器 | 字串 | 不透明的字串值表示任何特定 Blob 名稱之事件的邏輯順序。  使用者可使用標準字串比較，以了解 Blob 名稱相同之兩個事件的相對順序。 |
| storageDiagnostics | 物件 (object) | Azure 儲存體服務偶爾包含診斷資料。 出現時，事件消費者應該予以忽略。 |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|標題  |描述  |
|---------|---------|
| [快速入門：使用 Azure CLI 將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure CLI 將 Blob 儲存體事件傳送至 WebHook。 |
| [快速入門：使用 PowerShell 將 Blob 儲存體事件路由至自訂的 Web 端點](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 示範如何使用 Azure PowerShell 將 Blob 儲存體事件傳送至 WebHook。 |
| [快速入門：使用 Azure 入口網站建立和路由傳送 Blob 儲存體事件](blob-event-quickstart-portal.md) | 示範如何使用入口網站將 Blob 儲存體事件傳送至 WebHook。 |
| [Azure CLI：訂閱 Blob 儲存體帳戶的事件](./scripts/event-grid-cli-blob.md) | 訂閱 Blob 儲存體帳戶事件的範例指令碼。 它會將事件傳送至 WebHook。 |
| [PowerShell：訂閱 Blob 儲存體帳戶的事件](./scripts/event-grid-powershell-blob.md) | 訂閱 Blob 儲存體帳戶事件的範例指令碼。 它會將事件傳送至 WebHook。 |
| [Resource Manager 範本：建立 Blob 儲存體和訂用帳戶](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | 部署 Azure Blob 儲存體帳戶及訂閱該儲存體帳戶的事件。 它會將事件傳送到 WebHook。 |
| [概觀：回應 Blob 儲存體事件](../storage/blobs/storage-blob-event-overview.md) | 整合 Blob 儲存體與事件方格的概觀。 |

## <a name="next-steps"></a>下一步

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需有關使用 blob 儲存體事件的簡介，請參閱[路由 blob 儲存體事件 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 
