---
title: 搜索 Azure 資料存儲第 2 代（預覽版）
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure 資料存儲庫第 2 代中索引內容和中繼資料。 此功能當前處於公共預覽版
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905652"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Azure 資料存儲第 2 代中的索引文檔

> [!IMPORTANT] 
> Azure 資料存儲第 2 代支援當前處於公共預覽版中。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以通過填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來請求訪問預覽。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 當前沒有門戶或 .NET SDK 支援。


設置 Azure 存儲帳戶時，可以選擇啟用[階層命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 這允許將帳戶中的內容集合組織到目錄和嵌套子目錄的層次結構中。 通過啟用階層命名空間，可以啟用[Azure 資料湖存儲第 2 代](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)。

本文介紹如何開始索引 Azure 資料湖存儲 Gen2 中的文檔。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>設置 Azure 資料存儲湖存儲第 2 代索引子

您需要完成幾個步驟來索引來自資料存儲庫 Gen2 的內容。

### <a name="step-1-sign-up-for-the-preview"></a>第 1 步：註冊預覽版

填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)，註冊資料存儲庫第 2 代索引子預覽。 一旦您被接受預覽版，您將收到一封確認電子郵件。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>第 2 步：按照 Azure Blob 存儲索引設置步驟操作

收到預覽註冊成功確認後，即可創建索引管道。

您可以使用[REST API 版本 2019-05-06-預覽](search-api-preview.md)來索引資料存儲存儲 Gen2 的內容和中繼資料。 此時沒有門戶或 .NET SDK 支援。

資料湖存儲 Gen2 中的索引內容與 Azure Blob 存儲中的內容索引相同。 因此，要瞭解如何設置資料存儲源 Gen2 資料來源、索引和索引子，請參閱[如何使用 Azure 認知搜索在 Azure Blob 存儲中索引文檔](search-howto-indexing-azure-blob-storage.md)。 Blob 存儲文章還提供有關支援哪些文檔格式、提取哪些 Blob 中繼資料屬性、增量索引等的資訊。 對於資料存儲第 2 代，此資訊將相同。

## <a name="access-control"></a>存取控制

Azure 資料存儲單元 2 實現了一個[存取控制模型](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)，該模型同時支援基於 Azure 角色的存取控制 （RBAC） 和類似 POSIX 的存取控制清單 （ACL）。 索引資料湖存儲第 2 代中的內容時，Azure 認知搜索不會從內容中提取 RBAC 和 ACL 資訊。 因此，此資訊將不會包含在 Azure 認知搜索索引中。

如果對索引中的每個文檔保持存取控制很重要，則由應用程式開發人員實施[安全修整](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)。

## <a name="change-detection"></a>更改檢測

資料存儲湖存儲 Gen2 索引子支援更改檢測。 這意味著，當索引子運行時，它只會重新索引由 blob 的時間戳記確定的已更改的`LastModified`Blob。

> [!NOTE] 
> 資料存儲第 2 代允許重命名目錄。 重命名目錄時，該目錄中 Blob 的時間戳記不會更新。 因此，索引子不會重新索引這些 blob。 如果需要在目錄重命名後重新編制目錄中的 Blob，因為它們現在具有新的 URL，則需要更新目錄中所有 Blob`LastModified`的時間戳記，以便索引子知道在將來的運行期間重新索引它們。
