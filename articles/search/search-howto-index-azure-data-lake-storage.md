---
title: '搜尋 Azure Data Lake Storage Gen2 (preview) '
titleSuffix: Azure Cognitive Search
description: 瞭解如何在 Azure Data Lake Storage Gen2 中編制內容和中繼資料的索引。 這項功能目前為公開預覽狀態
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 4a585fa1c3c56d285859171e6b5673fe57beffb3
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517316"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>在 Azure Data Lake Storage Gen2 中編制檔索引

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 支援目前處於公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以填寫 [此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來要求存取預覽版。 [REST API 版本 2020-06-30-Preview](search-api-preview.md)和入口網站提供這項功能。 目前沒有 .NET SDK 支援。


設定 Azure 儲存體帳戶時，您可以選擇啟用 [階層式命名空間](../storage/blobs/data-lake-storage-namespace.md)。 這可將帳戶中的內容集合組織成目錄和嵌套子目錄的階層。 藉由啟用階層命名空間，您可以啟用 [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)。

本文說明如何開始使用 Azure Data Lake Storage Gen2 中的編制檔索引。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>設定 Azure Data Lake Storage Gen2 索引子

您必須完成幾個步驟，才能 Data Lake Storage Gen2 的索引內容。

### <a name="step-1-sign-up-for-the-preview"></a>步驟1：註冊預覽版

填寫 [此表單](https://aka.ms/azure-cognitive-search/indexer-preview)以註冊 Data Lake Storage Gen2 索引子預覽。 一旦接受預覽，您將會收到一封確認電子郵件。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>步驟2：遵循 Azure Blob 儲存體索引設定步驟

當您收到預覽註冊成功的確認之後，您就可以開始建立索引管線。

您可以使用 [REST API 2020-06-30-Preview 版](search-api-preview.md) 或入口網站，為 Data Lake Storage Gen2 的內容和中繼資料編制索引。 目前沒有 .NET SDK 支援。

在 Data Lake Storage Gen2 中編制內容索引與在 Azure Blob 儲存體中編制內容索引相同。 因此，若要瞭解如何設定 Data Lake Storage Gen2 資料來源、索引和索引子，請參閱 [如何使用 Azure 認知搜尋為 Azure Blob 儲存體中的檔編制索引](search-howto-indexing-azure-blob-storage.md)。 Blob 儲存體文章也會提供有關支援哪些檔案格式、哪些 Blob 中繼資料屬性已解壓縮、增量編制索引等的資訊。 這是 Data Lake Storage Gen2 的資訊。

## <a name="access-control"></a>存取控制

Azure Data Lake Storage Gen2 會實作為 [存取控制模型](../storage/blobs/data-lake-storage-access-control.md) ，以支援 azure 角色型存取控制 (azure RBAC) 和類似 POSIX 的存取控制清單 (acl) 。 從 Data Lake Storage Gen2 編制內容索引時，Azure 認知搜尋不會從內容中解壓縮 Azure RBAC 和 ACL 資訊。 因此，此資訊不會包含在您的 Azure 認知搜尋索引中。

如果在索引中維護每個檔的存取控制是很重要的，應用程式開發人員必須負責執行 [安全性](./search-security-trimming-for-azure-search.md)調整。

## <a name="change-detection"></a>變更偵測

Data Lake Storage Gen2 索引子支援變更偵測。 這表示當索引子執行時，它只會重新編制 blob 的時間戳記所決定的變更 blob `LastModified` 。

> [!NOTE] 
> Data Lake Storage Gen2 允許重新命名目錄。 當目錄重新命名時，該目錄中 blob 的時間戳記不會更新。 如此一來，索引子將不會重新編制這些 blob 的索引。 如果您需要在目錄重新命名後重新建立索引目錄中的 blob，因為它們現在有新的 Url，您將需要更新 `LastModified` 目錄中所有 blob 的時間戳記，讓索引子知道在未來執行時將其重新索引。