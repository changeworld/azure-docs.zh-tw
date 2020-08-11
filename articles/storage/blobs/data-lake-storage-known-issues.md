---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 了解 Azure Data Lake Storage Gen2 的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 07/21/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: 0309f4d07056e3986138139e10ab29faa675cfcd
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056073"
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

* 您無法同時使用 Blob API 和 Data Lake Storage API 來寫入檔案的相同執行個體。 如果使用 Data Lake Storage Gen2 API 來寫入檔案，則呼叫[取得區塊清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list) (機器翻譯) Blob API 將不會顯示該檔案的區塊。 您可使用 Data Lake Storage Gen2 API 或 Blob API 來覆寫檔案。 此操作不會影響檔案屬性。

* 當使用 [列出 Blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs) 作業而不指定分隔符號時，結果會同時包含目錄和 Blob。 如果選擇使用分隔符號，請僅使用正斜線 (`/`)。 這是唯一支援的分隔符號。

* 如果使用 [刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 來刪除目錄，則只有當該目錄為空時才會將其刪除。 這表示您無法以遞迴方式使用 Blob API 來刪除目錄。

不支援這些 Blob REST API：

* [Put Blob (頁面)](https://docs.microsoft.com/rest/api/storageservices/put-blob) (機器翻譯)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges) (機器翻譯)
* [累加複製 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob) (機器翻譯)
* [從 URL 放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url) (機器翻譯)
* [從 URL 附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url) (機器翻譯)

具有階層式命名空間的帳戶不支援非受控 VM 磁碟。 如果要在儲存體帳戶上啟用階層命名空間，請將非受控 VM 磁碟放入沒有啟用階層命名空間功能的儲存體帳戶。

<a id="api-scope-data-lake-client-library"></a>

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>SDK、PowerShell 和 Azure CLI 中的檔案系統支援

- 取得及設定 ACL 作業目前不是遞迴的。

<a id="known-issues-tools"></a>

## <a name="azcopy"></a>AzCopy

請僅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。 不支援舊版的 AzCopy (例如 AzCopy 8.1)。

<a id="storage-explorer"></a>

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

請僅使用  `1.6.0`  版或更高版本。

<a id="explorer-in-portal"></a>

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 入口網站中的儲存體總管

目前尚不支援 ACL。

<a id="third-party-apps"></a>

## <a name="thirdpartyapplications"></a>協力廠商應用程式

如果使用利用 REST API 的協力廠商應用程式，並將其與呼叫 Blob API 的 Data Lake Storage Gen2 應用程式搭配使用，其將會繼續運作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>存取控制清單 (ACL) 和匿名讀取權限

如果已將[匿名讀取權限](storage-manage-access-to-resources.md)授與容器，則 ACL 不會影響該容器或該容器中的檔案。

## <a name="premium-performance-blockblobstorage-storage-accounts"></a>進階效能 BlockBlobStorage 儲存體帳戶

### <a name="diagnostic-logs"></a>診斷記錄

診斷記錄尚無法使用 Azure 入口網站啟用。 您可使用 PowerShell 來加以啟用。 例如：

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

尚不支援保留天數的設定，但您可以使用任何支援的工具（例如 Azure 儲存體總管、REST 或 SDK）來手動刪除記錄。

### <a name="lifecycle-management-policies"></a>生命週期管理原則

- 進階 BlockBlobStorage 儲存體帳戶尚不支援生命週期管理原則。 

- 資料無法從進階層移至較低階層。 

- 目前尚不支援**刪除 Blob** 這項動作。 

### <a name="hdinsight-support"></a>HDInsight 支援

當建立 n 個 HDInsight 叢集時，您尚無法選取已在其上啟用階層命名空間功能的 BlockBlobStorage 帳戶。 不過，您可在建立叢集後將該帳戶連結至叢集。

### <a name="dremio-support"></a>Dremio 支援

Dremio 尚無法連線至已啟用階層命名空間功能的 BlockBlobStorage 帳戶。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure 儲存體 Blob (WASB) 驅動程式 (不支援 Data Lake Storage Gen2)

目前，僅設計用於 Blob API 的 WASB 驅動程式會在少數常見案例中遇到問題。 具體來說，當其是已啟用階層命名空間的儲存體帳戶用戶端時。 Data Lake Storage 上的多重通訊協定存取無法減輕這些問題。 

目前 (以及很可能在可預見的未來內) 不支援使用 WASB 驅動程式作為已啟用階層命名空間的儲存體帳戶用戶端。 反之，我們建議在 Hadoop 環境中選擇使用 [Azure Blob 檔案系統 (ABFS)](data-lake-storage-abfs-driver.md) 驅動程式。 如果要嘗試使用早於 Hadoop 分支 3 版本的內部部署 Hadoop 環境進行移轉，請開啟 Azure 支援票證以供連絡來為您和組織指出正確的方向。
