---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 瞭解 Azure Data Lake Storage Gen2 的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 7d637c2fb3f4a4d5f8deac9cd99c0a44af6568e6
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359737"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文說明 Azure Data Lake Storage Gen2 的限制和已知問題。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

已增加的 Blob 儲存體功能數目現在可與具有階層式命名空間的帳戶搭配使用。 如需完整清單，請參閱[Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務整合

Data Lake Storage gen2 支援數個 Azure 服務，可用於內嵌資料、執行分析，以及建立視覺表示法。 如需支援的 Azure 服務清單，請參閱[支援 Azure Data Lake Storage Gen2 的 azure 服務](data-lake-storage-supported-azure-services.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 如需完整清單，請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平臺](data-lake-storage-supported-open-source-platforms.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平臺](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob Api 和 Data Lake Storage Gen2 Api 可以在相同的資料上運作。

本節說明使用 blob Api 和 Data Lake Storage Gen2 Api 來操作相同資料的問題和限制。

* 您無法同時使用 Blob Api 和 Data Lake Storage Api 來寫入相同的檔案實例。 如果您使用 Data Lake Storage Gen2 Api 來寫入檔案，則對[Get 區塊清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的呼叫將不會顯示該檔案的區塊。 您可以使用 Data Lake Storage Gen2 Api 或 Blob Api 來覆寫檔案。 這不會影響檔案屬性。

* 當您使用[列出 blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs)作業但未指定分隔符號時，結果會包含目錄和 blob。 如果您選擇使用分隔符號，請只使用正斜線（`/`）。 這是唯一支援的分隔符號。

* 如果您使用「[刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API」來刪除目錄，則只有當該目錄是空的時，才會將它刪除。 這表示您無法以遞迴方式使用 Blob API 刪除目錄。

不支援這些 Blob REST Api：

* [Put Blob （頁面）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [增量複製 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [從 URL 放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob （Append）](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [從 URL 附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有階層式命名空間的帳戶不支援非受控 VM 磁片。 如果您想要在儲存體帳戶上啟用階層式命名空間，請將非受控 VM 磁片放入未啟用階層命名空間功能的儲存體帳戶。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>Sdk 中的檔案系統支援

- [.Net](data-lake-storage-directory-file-acl-dotnet.md)、 [JAVA](data-lake-storage-directory-file-acl-java.md)和[Python](data-lake-storage-directory-file-acl-python.md)，以及[JavaScript](data-lake-storage-directory-file-acl-javascript.md)和支援均為公開預覽狀態。 目前不支援其他 Sdk。
- 取得和設定 ACL 作業目前不是遞迴的。

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 和 Azure CLI 中的檔案系統支援

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI](data-lake-storage-directory-file-acl-cli.md)支援現供公開預覽。
- 取得和設定 ACL 作業目前不是遞迴的。

## <a name="lifecycle-management-policies"></a>生命週期管理原則

* 尚不支援刪除 blob 快照集。  

* 目前有一些 bug 會影響生命週期管理原則和封存存取層。 

## <a name="diagnostic-logs"></a>診斷記錄

Azure 儲存體總管 1.10. x 無法用於查看診斷記錄。 若要查看記錄，請使用 AzCopy 或 Sdk。

## <a name="blobfuse"></a>Blobfuse

不支援 Blobfuse。



<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

只使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。 不支援舊版的 AzCopy （例如 AzCopy 8.1）。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

僅使用  或更高版本 `1.6.0`。目前有一個會影響版本 `1.11.0` 的儲存體 bug，在某些情況下可能會導致驗證錯誤。 即將推出儲存體錯誤的修正程式，但作為因應措施，建議您使用 [免費下載](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes) 提供的版本 `1.10.x`。 `1.10.x`的  不會受到儲存體 bug 的影響。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>在 Azure 入口網站中儲存體總管

尚不支援 Acl。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>協力廠商應用程式

使用 REST Api 來工作的協力廠商應用程式，如果您將其與呼叫 Blob Api 的 Data Lake Storage Gen2 應用程式搭配使用，將會繼續工作。





