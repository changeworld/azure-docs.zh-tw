---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 瞭解 Azure 資料存儲第 2 代的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 4f8fae6580272ed53b8d440ba3e74c6a1ed1e61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061518"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文介紹了 Azure 資料存儲第 2 代的限制和已知問題。

## <a name="supported-blob-storage-features"></a>支援的 Blob 存儲功能

越來越多的 Blob 存儲功能現在使用具有階層命名空間的帳戶。 有關完整清單，請參閱[Azure 資料湖存儲第 2 代 中可用的 Blob 存儲功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務集成

Data Lake Storage gen2 支援數個 Azure 服務，可用於內嵌資料、執行分析，以及建立視覺表示法。 有關受支援的 Azure 服務的清單，請參閱[支援 Azure 資料存儲湖存儲 Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

請參閱[支援 Azure 資料存儲第 2 代](data-lake-storage-supported-azure-services.md)的 Azure 服務。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 有關完整清單，請參閱[支援 Azure 資料存儲第 2 代 的開源平臺](data-lake-storage-supported-open-source-platforms.md)。

請參閱[支援 Azure 資料存儲第 2 代 的開源平臺](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob API 和資料存儲第 2 代 API 可以對相同的資料進行操作。

本節介紹使用 blob API 和資料存儲存儲 Gen2 API 對同一資料進行操作的問題和限制。

* 不能同時使用 Blob API 和資料湖存儲 API 寫入檔的同一實例。 如果使用 Data Lake 存儲 Gen2 API 寫入檔，則對[獲取阻止清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的調用將看不到該檔的塊。 您可以使用資料湖存儲 Gen2 API 或 Blob API 覆蓋檔。 這不會影響檔案屬性。

* 使用[清單 Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs)操作而不指定分隔符號時，結果將同時包括目錄和 Blob。 如果選擇使用分隔符號，則僅使用正向斜杠 （）。`/` 這是唯一受支援的分隔符號。

* 如果使用[刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 刪除目錄，則僅當該目錄為空時才會刪除該目錄。 這意味著您不能遞迴使用 Blob API 刪除目錄。

不支援這些 Blob REST API：

* [放置 Blob（頁）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量複製 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [從 URL 中放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [放置 Blob（追加）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [URL 中的追加塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有階層命名空間的帳戶不支援非託管 VM 磁片。 如果要在存儲帳戶上啟用階層命名空間，則將非託管 VM 磁片放入未啟用階層命名空間功能的存儲帳戶中。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>SDK 中的檔案系統支援

獲取和設置 ACL 操作當前不是遞迴的。

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的檔案系統支援

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI](data-lake-storage-directory-file-acl-cli.md)支援處於公共預覽版中。
- 獲取和設置 ACL 操作當前不是遞迴的。

## <a name="lifecycle-management-policies"></a>生命週期管理原則

* 尚不支援刪除 Blob 快照。  

## <a name="archive-tier"></a>存檔層

當前存在影響存檔訪問層的錯誤。


## <a name="blobfuse"></a>Blobfuse

不支援 Blobfuse。

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

僅使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。不支援早期版本的 AzCopy，如 AzCopy v8.1。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

僅使用版本 `1.6.0` 或更高版本。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 門戶中的存儲資源管理器

尚未支援 ACL。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>協力廠商申請

如果使用 REST API 工作的協力廠商應用程式，如果將其與調用 Blob API 的資料存儲存儲 Gen2 應用程式一起使用，則它們可能會正常工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>存取控制清單 （ACL） 和匿名讀取存取

如果已授予對容器的[匿名讀取存取許可權](storage-manage-access-to-resources.md)，則 ACL 對該容器或該容器中的檔沒有影響。

## <a name="windows-azure-storage-blob-wasb-driver"></a>Windows Azure 存儲 Blob （WASB） 驅動程式

目前，與使用 WASB 驅動程式以及具有階層命名空間的帳戶相關的幾個問題。 我們建議您在工作負荷中使用[Azure Blob 檔案系統 （ABFS）](data-lake-storage-abfs-driver.md)驅動程式。 





