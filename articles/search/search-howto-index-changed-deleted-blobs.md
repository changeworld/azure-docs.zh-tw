---
title: 已變更和刪除的 blob
titleSuffix: Azure Cognitive Search
description: 從 Azure Blob 儲存體匯入的初始搜尋索引建立之後，後續的索引編制只會收取變更或刪除的 blob。 本文說明詳細資料。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2e73039418233c97fc20242ed7af7df14c5b47ee
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534772"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>如何在 Azure 認知搜尋索引中設定 blob 的變更和刪除偵測

建立初始搜尋索引之後，您可能會想要設定後續的索引子工作，只挑選初始執行後已建立或刪除的檔。 針對源自 Azure Blob 儲存體的搜尋內容，當您使用排程來觸發編制索引時，會自動進行變更偵測。 根據預設，服務只會重新編制已變更的 blob，如 blob 的 `LastModified` 時間戳記所決定。 相較于搜尋索引子支援的其他資料來源，blob 一律會有時間戳記，因此不需要手動設定變更偵測原則。

雖然變更偵測是指定的，但不會刪除偵測。 如果您想要偵測已刪除的檔，請務必使用「虛刪除」方法。 如果您直接刪除 blob，對應的文件將不會在搜尋索引中移除。

有兩種方式可以實行虛刪除方法。 兩者都如下所述。

## <a name="native-blob-soft-delete-preview"></a>原生 Blob 虛刪除 (預覽)

> [!IMPORTANT]
> 原生 blob 虛刪除支援目前為預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 2020-06-30-Preview 版](./search-api-preview.md)提供這項功能。 目前沒有入口網站或 .NET SDK 支援。

> [!NOTE]
> 使用原生 blob 虛刪除原則時，索引中的檔索引鍵必須是 blob 屬性或 blob 中繼資料。

在此方法中，您將使用 Azure Blob 儲存體所提供的 [原生 blob 虛刪除](../storage/blobs/soft-delete-blob-overview.md) 功能。 如果您的儲存體帳戶已啟用原生 blob 虛刪除，則您的資料來源會設定原生虛刪除原則，而索引子會尋找已轉換為虛刪除狀態的 blob，索引子會從索引中移除該檔。 從 Azure Data Lake Storage Gen2 為 blob 編制索引時，不支援原生 blob 虛刪除原則。

請使用下列步驟：

1. [針對 Azure Blob 儲存體啟用原生虛刪除](../storage/blobs/soft-delete-blob-overview.md)。 建議您將保留原則設定為比索引子間隔排程更高的值。 如此一來，如果執行索引子時發生問題，或如果您有大量的檔要編制索引，則索引子會有很多時間最後處理虛刪除的 blob。 如果 blob 處於虛刪除狀態，Azure 認知搜尋索引子只會刪除索引中的檔。

1. 在資料來源上設定原生 blob 虛刪除偵測原則。 範例如下所示。 因為這項功能處於預覽狀態，所以您必須使用預覽版 REST API。

1. 執行索引子，或將索引子設定為依排程執行。 當索引子執行並處理 blob 時，會從索引中移除檔。

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>使用原生虛刪除原則 (重新編制刪除的 blob 索引) 

如果您從 Azure Blob 儲存體中刪除已在儲存體帳戶上啟用原生虛刪除的 blob，blob 將會轉換為虛刪除狀態，讓您可以選擇在保留期限內取消刪除該 blob。 如果您在索引子處理之後反轉刪除，索引子將不會一律為還原的 blob 編制索引。 這是因為索引子會根據 blob 的時間戳記來決定要編制索引的 blob `LastModified` 。 未刪除虛刪除的 blob 時，其 `LastModified` 時間戳記不會更新，因此如果索引子已處理具有較新時間戳記的 blob `LastModified` ，則不會重新索引未刪除的 blob。 

若要確定未刪除的 blob 重新建立索引，您必須更新 blob 的 `LastModified` 時間戳記。 其中一個方法是重新儲存該 blob 的中繼資料。 您不需要變更中繼資料，但重新儲存中繼資料會更新 blob 的 `LastModified` 時間戳記，讓索引子知道它需要重新索引此 blob。

## <a name="soft-delete-using-custom-metadata"></a>使用自訂中繼資料的虛刪除

在此方法中，您將使用 blob 的中繼資料，來指出何時應從搜尋索引中移除檔。 此方法需要兩個不同的動作，從索引中刪除搜尋檔，然後在 Azure 儲存體中刪除 blob。

請使用下列步驟：

1. 將自訂中繼資料索引鍵/值組新增至 blob，以指出 Azure 認知搜尋它會以邏輯方式刪除。

1. 在資料來源上設定虛刪除資料行偵測原則。 範例如下所示。

1. 一旦索引子處理 blob，並從索引中刪除檔之後，您就可以刪除 Azure Blob 儲存體中的 blob。

例如，如果 blob 有值為 `true` 的中繼資料屬性 `IsDeleted`，則下列原則會認為 blob 已刪除：

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>使用自訂中繼資料) 將取消刪除的 blob 重新編制索引 (

在索引子處理已刪除的 blob，並從索引中移除對應的搜尋檔之後，如果 blob 的 `LastModified` 時間戳記早于最後一次執行的索引子，它就不會重新流覽該 blob。

如果您想要將該檔重新編制索引，請變更該 blob 的， `"softDeleteMarkerValue" : "false"` 然後重新執行索引子。

## <a name="help-us-make-azure-cognitive-search-better"></a>協助我們改善 Azure 認知搜尋

如果您有功能要求或改進的想法，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 提供。 如果您需要使用現有功能的協助，請將您的問題張貼在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上。

## <a name="next-steps"></a>後續步驟

* [Azure 認知搜尋中的索引子](search-indexer-overview.md)
* [如何設定 blob 索引子](search-howto-indexing-azure-blob-storage.md)
* [Blob 索引編制總覽](search-blob-storage-integration.md)