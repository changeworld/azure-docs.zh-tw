---
title: 搜尋 Azure Data Lake Storage Gen2 （預覽）
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure Data Lake Storage Gen2 中編制內容和中繼資料的索引。 這項功能目前為公開預覽狀態
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905652"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>在 Azure Data Lake Storage Gen2 中編制檔的索引

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 支援目前處於公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來要求預覽的存取權。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。


設定 Azure 儲存體帳戶時，您可以選擇啟用[階層式命名空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)。 這可讓您將帳戶中的內容集合組織成目錄和嵌套子目錄的階層結構。 藉由啟用階層命名空間，您就可以啟用[Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)。

本文說明如何開始編制 Azure Data Lake Storage Gen2 中的檔索引。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>設定 Azure Data Lake Storage Gen2 索引子

您必須完成幾個步驟，才能從 Data Lake Storage Gen2 編制內容的索引。

### <a name="step-1-sign-up-for-the-preview"></a>步驟1：註冊預覽版

填寫[這份表單](https://aka.ms/azure-cognitive-search/indexer-preview)，以註冊 Data Lake Storage Gen2 索引子預覽。 一旦您接受預覽後，您會收到確認電子郵件。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>步驟2：遵循 Azure Blob 儲存體索引設定步驟

一旦收到確認您的預覽註冊成功之後，您就可以開始建立索引管線。

您可以使用[REST API 版本 2019-05-06-Preview](search-api-preview.md)，從 Data Lake Storage Gen2 編制內容和中繼資料的索引。 目前沒有入口網站或 .NET SDK 支援。

在 Data Lake Storage Gen2 中編制內容的索引，等同于在 Azure Blob 儲存體中編制內容的索引。 因此，若要瞭解如何設定 Data Lake Storage Gen2 資料來源、索引和索引子，請參閱[如何使用 Azure 認知搜尋在 Azure Blob 儲存體中編制檔索引](search-howto-indexing-azure-blob-storage.md)。 Blob 儲存體一文也提供支援哪些檔案格式、哪些 Blob 中繼資料屬性已解壓縮、累加編制索引等等的相關資訊。 Data Lake Storage Gen2 的此資訊將會相同。

## <a name="access-control"></a>存取控制

Azure Data Lake Storage Gen2 會實作用於支援 Azure 角色型存取控制（RBAC）和 POSIX 型存取控制清單（Acl）的[存取控制模型](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)。 從 Data Lake Storage Gen2 編制內容的索引時，Azure 認知搜尋不會從內容中解壓縮 RBAC 和 ACL 資訊。 因此，此資訊不會包含在您的 Azure 認知搜尋索引中。

如果在索引中維護每份檔的存取控制十分重要，則由應用程式開發人員負責執行[安全性](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)調整。

## <a name="change-detection"></a>變更偵測

Data Lake Storage Gen2 索引子支援變更偵測。 這表示當索引子執行時，它只會重新編制由 blob 的`LastModified`時間戳記決定的已變更 blob。

> [!NOTE] 
> Data Lake Storage Gen2 允許重新命名目錄。 當目錄重新命名時，該目錄中 blob 的時間戳記不會更新。 因此，索引子將不會重新編制這些 blob 的索引。 如果您需要在目錄重新命名之後重新建立索引目錄中的 blob，因為它們現在有新的 Url，您將需要更新目錄中`LastModified`所有 blob 的時間戳記，讓索引子知道在未來執行期間將它們重新編制索引。
