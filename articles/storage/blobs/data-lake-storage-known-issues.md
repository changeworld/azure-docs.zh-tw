---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 10/28/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 32f8ab3f7e222108bec4ec81b14c113705370096
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913057"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文描述 Azure Data Lake Storage Gen2 的限制和已知問題。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

持續增加的 Blob 儲存體功能，現在可用於具有階層式命名空間的帳戶。 如需完整清單，請參閱 [Azure Data Lake Storage Gen2 中可用的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務整合

Azure Data Lake Storage Gen2 支援數個 Azure 服務，其可用於內嵌資料、執行分析，以及建立視覺表示法。 如需支援的 Azure 服務清單，請參閱[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 如需完整清單，請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平台](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob API 和 Data Lake Storage Gen2 API 可運作於相同的資料上。

本節描述使用 Blob API 和 Data Lake Storage Gen2 API 來對相同資料進行操作時的問題和限制。

* 您無法使用 blob API 和 Data Lake Storage Api 寫入相同的檔案實例。 如果使用 Data Lake Storage Gen2 API 來寫入檔案，則呼叫[取得區塊清單](/rest/api/storageservices/get-block-list) (機器翻譯) Blob API 將不會顯示該檔案的區塊。 唯一的例外是當您使用覆寫時。 您可以使用任一種 API 來覆寫檔案/blob。

* 當使用 [列出 Blob](/rest/api/storageservices/list-blobs) 作業而不指定分隔符號時，結果會同時包含目錄和 Blob。 如果選擇使用分隔符號，請僅使用正斜線 (`/`)。 這是唯一支援的分隔符號。

* 如果使用 [刪除 Blob](/rest/api/storageservices/delete-blob) API 來刪除目錄，則只有當該目錄為空時才會將其刪除。 這表示您無法以遞迴方式使用 Blob API 來刪除目錄。

不支援這些 Blob REST API：

* [Put Blob (頁面)](/rest/api/storageservices/put-blob) (機器翻譯)
* [放置頁面](/rest/api/storageservices/put-page)
* [取得頁面範圍](/rest/api/storageservices/get-page-ranges) (機器翻譯)
* [累加複製 Blob](/rest/api/storageservices/incremental-copy-blob) (機器翻譯)
* [從 URL 放置頁面](/rest/api/storageservices/put-page-from-url) (機器翻譯)
* [附加區塊](/rest/api/storageservices/append-block)
* [從 URL 附加區塊](/rest/api/storageservices/append-block-from-url) (機器翻譯)


具有階層式命名空間的帳戶不支援非受控 VM 磁碟。 如果要在儲存體帳戶上啟用階層命名空間，請將非受控 VM 磁碟放入沒有啟用階層命名空間功能的儲存體帳戶。

<a id="api-scope-data-lake-client-library"></a>

## <a name="support-for-setting-access-control-lists-acls-recursively"></a>支援以遞迴方式將存取控制清單設定 (Acl) 

將 ACL 變更以遞迴方式從父目錄套用到子專案的能力已正式運作。 在這項功能的目前版本中，您可以使用 PowerShell、Azure CLI 和 .NET、JAVA 和 Python SDK 來套用 ACL 變更。 Azure 入口網站或 Azure 儲存體總管尚未提供支援。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

請僅使用最新版本的 AzCopy ([AzCopy v10](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。  不支援舊版的 AzCopy (例如 AzCopy 8.1)。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

只使用版本 `1.6.0` 或更高版本。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 入口網站中的儲存體總管

目前尚不支援 ACL。

<a id="third-party-apps"></a>

## <a name="third-party-applications"></a>協力廠商應用程式

如果使用利用 REST API 的協力廠商應用程式，並將其與呼叫 Blob API 的 Data Lake Storage Gen2 應用程式搭配使用，其將會繼續運作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>存取控制清單 (ACL) 和匿名讀取權限

如果已將[匿名讀取權限](./anonymous-read-access-configure.md)授與容器，則 ACL 不會影響該容器或該容器中的檔案。

## <a name="diagnostic-logs"></a>診斷記錄

尚不支援保留天數的設定，但您可以使用任何支援的工具（例如 Azure 儲存體總管、REST 或 SDK）手動刪除記錄。

## <a name="lifecycle-management-policies-with-premium-tier-for-azure-data-lake-storage"></a>使用進階層進行 Azure Data Lake Storage 的生命週期管理原則

您無法在經常性存取層、非經常性存取層和封存層之間移動儲存在進階層中的資料。 不過，您可以將資料從進階層複製到不同帳戶中的經常性存取層。

## <a name="dremio-support-with-premium-performance-blockblobstorage-storage-accounts"></a>Premium 效能 BlockBlobStorage 儲存體帳戶的 Dremio 支援

Dremio 尚無法連線至已啟用階層命名空間功能的 BlockBlobStorage 帳戶。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure 儲存體 Blob (WASB) 驅動程式 (不支援 Data Lake Storage Gen2)

目前，僅設計用於 Blob API 的 WASB 驅動程式會在少數常見案例中遇到問題。 具體來說，當其是已啟用階層命名空間的儲存體帳戶用戶端時。 Data Lake Storage 上的多重通訊協定存取無法減輕這些問題。 

目前 (以及很可能在可預見的未來內) 不支援使用 WASB 驅動程式作為已啟用階層命名空間的儲存體帳戶用戶端。 反之，我們建議在 Hadoop 環境中選擇使用 [Azure Blob 檔案系統 (ABFS)](data-lake-storage-abfs-driver.md) 驅動程式。 如果要嘗試使用早於 Hadoop 分支 3 版本的內部部署 Hadoop 環境進行移轉，請開啟 Azure 支援票證以供連絡來為您和組織指出正確的方向。