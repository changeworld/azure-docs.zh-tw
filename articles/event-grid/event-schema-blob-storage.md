---
title: Azure Event Grid blob 儲存體事件結構描述
description: 描述 Azure Event Grid blob 儲存體事件的屬性
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 4a71f50a130bd9b22965d39fa942b47c70857a86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265034"
---
# <a name="azure-event-grid-event-schema-for-blob-storage"></a>blob 儲存體的 Azure Event Grid 事件結構描述

本文提供 blob 儲存體事件的屬性與結構描述。如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

如需範例指令碼和教學課程的清單，請參閱[儲存體事件來源](event-sources.md#storage)。

>[!NOTE]
> 只有種類為 [StorageV2 (一般用途 v2)]**** 和 [BlobStorage]**** 的儲存體帳戶支援事件整合。 **儲存體 (一般用途 v1)** 不** 支援與事件方格整合。

## <a name="list-of-events-for-blob-rest-apis"></a>Blob REST API 的事件清單

當用戶端通過調用 Blob REST API 創建、替換或刪除 Blob 時，將觸發這些事件。

 |事件名稱 |描述|
 |----------|-----------|
 |**Microsoft.Storage.BlobCreated** |創建或替換 Blob 時觸發。 <br>具體而言，當用戶端使用`PutBlob``PutBlockList``CopyBlob`Blob REST API 中可用的 操作時，將觸發此事件。   |
 |**Microsoft.Storage.BlobDeleted** |刪除 Blob 時觸發。 <br>具體而言，當用戶端調用 Blob REST `DeleteBlob` API 中可用的操作時，將觸發此事件。 |

> [!NOTE]
> 如果要確保僅在完全提交塊 Blob 時觸發**Microsoft.Storage.Blob 創建**事件，請篩選`CopyBlob`、`PutBlob`和`PutBlockList`REST API 呼叫的事件。 這些 API 呼叫僅在資料完全提交到塊 Blob 後觸發**Microsoft.Storage.Blob 創建**事件。 要瞭解如何創建篩選器，請參閱[事件網格的篩選事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

## <a name="list-of-the-events-for-azure-data-lake-storage-gen-2-rest-apis"></a>Azure 資料存儲第 2 代 REST API 的事件清單

如果在存儲帳戶上啟用階層命名空間，並且用戶端調用 Azure 資料湖存儲 Gen2 REST API，則觸發這些事件。

|事件名稱|描述|
|----------|-----------|
|**Microsoft.Storage.BlobCreated** | 創建或替換 Blob 時觸發。 <br>具體而言，當用戶端使用 Azure 資料`CreateFile`湖`FlushWithClose`存儲 Gen2 REST API 中可用的 和 操作時，將觸發此事件。 |
|**Microsoft.Storage.BlobDeleted** |刪除 Blob 時觸發。 <br>具體而言，當用戶端調用 Azure 資料湖`DeleteFile`存儲 Gen2 REST API 中可用的操作時，也會觸發此事件。 |
|**微軟.存儲.Blob 重新命名**|重命名 Blob 時觸發。 <br>具體而言，當用戶端使用 Azure 資料`RenameFile`湖存儲 Gen2 REST API 中可用的操作時，將觸發此事件。|
|**微軟.存儲.目錄創建**|創建目錄時觸發。 <br>具體而言，當用戶端使用 Azure 資料`CreateDirectory`湖存儲 Gen2 REST API 中可用的操作時，將觸發此事件。|
|**微軟.存儲.目錄重新命名**|重命名目錄時觸發。 <br>具體而言，當用戶端使用 Azure 資料`RenameDirectory`湖存儲 Gen2 REST API 中可用的操作時，將觸發此事件。|
|**微軟.存儲.目錄已刪除**|刪除目錄時觸發。 <br>具體而言，當用戶端使用 Azure 資料`DeleteDirectory`湖存儲 Gen2 REST API 中可用的操作時，將觸發此事件。|

> [!NOTE]
> 如果要確保**Microsoft.Storage.Blob創建**事件僅在完全提交塊 Blob 時觸發，請篩選`FlushWithClose`REST API 呼叫的事件。 此 API 呼叫僅在資料完全提交到塊 Blob 後觸發**Microsoft.Storage.Blob 創建**事件。 要瞭解如何創建篩選器，請參閱[事件網格的篩選事件](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)。

<a id="example-event" />

## <a name="the-contents-of-an-event-response"></a>事件回應的內容

觸發事件時，事件網格服務會將有關該事件的資料發送到訂閱終結點。

本節包含該資料在每個 Blob 存儲事件的外觀示例。

### <a name="microsoftstorageblobcreated-event"></a>微軟.存儲.Blob 創建事件

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

### <a name="microsoftstorageblobcreated-event-data-lake-storage-gen2"></a>微軟.存儲.Blob 創建事件（資料湖存儲第 2 代）

如果 Blob 存儲帳戶具有階層命名空間，則資料看起來與上一個示例類似，但這些更改除外：

* 鍵`dataVersion`設置為`2`的值。

* 鍵`data.api`設置為字串`CreateFile`或`FlushWithClose`。

* 該`contentOffset`鍵包含在資料集中。

> [!NOTE]
> 如果應用程式使用該`PutBlockList`操作將新 Blob 上載到帳戶，則資料將不包含這些更改。

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

### <a name="microsoftstorageblobdeleted-event"></a>微軟.存儲.Blob 刪除事件

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

### <a name="microsoftstorageblobdeleted-event-data-lake-storage-gen2"></a>微軟.存儲.Blob 刪除事件（資料湖存儲第 2 代）

如果 Blob 存儲帳戶具有階層命名空間，則資料看起來與上一個示例類似，但這些更改除外：

* 鍵`dataVersion`設置為`2`的值。

* 鍵`data.api`設置為字串`DeleteFile`。

* 鍵`url`包含路徑`dfs.core.windows.net`。

> [!NOTE]
> 如果應用程式使用`DeleteBlob`該操作從帳戶中刪除 Blob，則資料將不包含這些更改。

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

### <a name="microsoftstorageblobrenamed-event"></a>微軟.存儲.Blob 重新命名事件

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

### <a name="microsoftstoragedirectorycreated-event"></a>微軟.存儲.目錄創建事件

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

### <a name="microsoftstoragedirectoryrenamed-event"></a>微軟.存儲.目錄重新命名事件

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

### <a name="microsoftstoragedirectorydeleted-event"></a>微軟.存儲.目錄刪除事件

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

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| 主題 | 字串 | 事件來源的完整資源路徑。 此欄位不可寫入。 Event Grid 提供此值。 |
| subject | 字串 | 發行者定義事件主體的路徑。 |
| eventType | 字串 | 此事件來源已註冊的事件類型之一。 |
| eventTime | 字串 | 事件產生的時間，以提供者之 UTC 時間為準。 |
| id | 字串 | 事件的唯一識別碼。 |
| data | 物件 (object) | blob 儲存體帳戶。 |
| dataVersion | 字串 | 資料物件的結構描述版本。 發行者會定義結構描述版本。 |
| metadataVersion | 字串 | 事件中繼資料的結構描述版本。 Event Grid 會定義最上層屬性的結構描述。 Event Grid 提供此值。 |

資料物件具有下列屬性：

| 屬性 | 類型 | 描述 |
| -------- | ---- | ----------- |
| api | 字串 | 觸發事件的作業。 |
| clientRequestId | 字串 | 存儲 API 操作的用戶端提供的請求 ID。 此 ID 可用於使用日誌中的"用戶端-請求-id"欄位與 Azure 存儲診斷日誌相關聯，並且可以使用"x-ms-用戶端-請求-id"標頭在用戶端請求中提供。 請參閱[記錄格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| requestId | 字串 | 儲存體 API 作業由服務產生的要求識別碼。 可用於利用記錄中的 "request-id-header" 欄位與 Azure 儲存體診斷記錄建立關聯，並從 'x-ms-request-id' 標頭中的 API 呼叫初始化傳回。 請參閱[記錄格式](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format)。 |
| etag | 字串 | 此值可讓您依條件執行作業。 |
| ContentType | 字串 | 為 blob 指定內容類型。 |
| contentLength | integer | Blob 大小 (以位元組為單位)。 |
| blobType | 字串 | Blob 的類型。 有效值為 "BlockBlob" 或 "PageBlob"。 |
| contentOffset | number | 在事件觸發應用程式完成寫入檔的點處採用寫入操作的偏移（以位元組為單位）。 <br>僅出現在具有階層命名空間的 Blob 存儲帳戶上觸發的事件。|
| 目的地Url |字串 | 操作完成後將存在的檔的 URL。 例如，如果重命名了檔，則`destinationUrl`屬性包含新檔案名的 URL。 <br>僅出現在具有階層命名空間的 Blob 存儲帳戶上觸發的事件。|
| 源Url |字串 | 操作之前存在的檔的 URL。 例如，如果重命名了檔，則`sourceUrl`包含重命名操作之前原始檔案名的 URL。 <br>僅出現在具有階層命名空間的 Blob 存儲帳戶上觸發的事件。 |
| url | 字串 | blob 的路徑。 <br>如果用戶端使用 Blob REST API，則 URL 具有此結構：*\<存儲帳戶名稱\>\<.blob.core.windows.net/容器名稱\>/\<檔案名\>*。 <br>如果用戶端使用 Data Lake 存儲 REST API，則 URL 具有此結構：*\<存儲\>帳戶名稱\<.dfs.core.windows.net/檔案系統名稱\>/\<檔案名\>*。 |
| 遞迴 | 字串 | `True`對所有子目錄執行操作;否則`False`。 <br>僅出現在具有階層命名空間的 Blob 存儲帳戶上觸發的事件。 |
| 排序器 | 字串 | 不透明的字串值表示任何特定 Blob 名稱之事件的邏輯順序。  使用者可使用標準字串比較，以了解 Blob 名稱相同之兩個事件的相對順序。 |
| storageDiagnostics | 物件 (object) | Azure 儲存體服務偶爾包含診斷資料。 出現時，事件消費者應該予以忽略。 |

## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是 Event Grid？](overview.md)
* 若要了解 Event Grid 訂用帳戶的建立，請參閱 [Event Grid 訂用帳戶結構描述](subscription-creation-schema.md)。
* 如需有關使用 blob 儲存體事件的簡介，請參閱[路由 blob 儲存體事件 - Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)。 
