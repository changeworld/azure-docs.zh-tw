---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 瞭解 Azure Data Lake Storage Gen2 的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 05/10/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: b02835ae3a1d7fed52f2cdb4ab25aa74ba66e8c3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83119887"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文說明 Azure Data Lake Storage Gen2 的限制和已知問題。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

已增加的 Blob 儲存體功能數目現在可與具有階層式命名空間的帳戶搭配使用。 如需完整清單，請參閱[Azure Data Lake Storage Gen2 中提供的 Blob 儲存體功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務整合

Azure Data Lake Storage Gen2 支援數個 Azure 服務，可讓您用來內嵌資料、執行分析，以及建立視覺標記法。 如需支援的 Azure 服務清單，請參閱[支援 Azure Data Lake Storage Gen2 的 azure 服務](data-lake-storage-supported-azure-services.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 如需完整清單，請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平臺](data-lake-storage-supported-open-source-platforms.md)。

請參閱[支援 Azure Data Lake Storage Gen2 的開放原始碼平臺](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob Api 和 Data Lake Storage Gen2 Api 可以在相同的資料上運作。

本節說明使用 blob Api 和 Data Lake Storage Gen2 Api 來操作相同資料的問題和限制。

* 您不能同時使用 Blob Api 和 Data Lake Storage Api 來寫入相同的檔案實例。 如果您使用 Data Lake Storage Gen2 Api 來寫入檔案，則對[Get 區塊清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的呼叫將不會顯示該檔案的區塊。 您可以使用 Data Lake Storage Gen2 Api 或 Blob Api 來覆寫檔案。 這不會影響檔案屬性。

* 當您使用[列出 blob](https://docs.microsoft.com/rest/api/storageservices/list-blobs)作業但未指定分隔符號時，結果會包含目錄和 blob。 如果您選擇使用分隔符號，請只使用正斜線（ `/` ）。 這是唯一支援的分隔符號。

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

## <a name="file-system-support-in-sdks-powershell-and-azure-cli"></a>Sdk、PowerShell 和 Azure CLI 中的檔案系統支援

- 取得和設定 ACL 作業目前不是遞迴的。


## <a name="lifecycle-management-policies"></a>生命週期管理原則

尚不支援刪除 blob 快照集。 

## <a name="archive-tier"></a>封存層

目前有一個 bug 會影響封存存取層。

## <a name="blobfuse"></a>Blobfuse

不支援 Blobfuse。

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

只使用最新版本的 AzCopy （[AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)）。不支援舊版的 AzCopy （例如 AzCopy 8.1）。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

僅使用版本  `1.6.0`   或更高版本。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 入口網站中的儲存體總管

尚不支援 Acl。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>協力廠商應用程式

使用 REST Api 來工作的協力廠商應用程式，如果您將其與呼叫 Blob Api 的 Data Lake Storage Gen2 應用程式搭配使用，將會繼續工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>存取控制清單（ACL）和匿名讀取權限

如果已授與容器的[匿名讀取權限](storage-manage-access-to-resources.md)，則 acl 不會影響該容器或該容器中的檔案。

## <a name="premium-performance-block-blob-storage-accounts"></a>Premium-效能區塊 blob 儲存體帳戶

### <a name="diagnostic-logs"></a>診斷記錄

診斷記錄尚未使用 Azure 入口網站啟用。 您可以使用 PowerShell 來啟用它們。 例如：

```powershell
#To login
Connect-AzAccount

#Set default block blob storage account.
Set-AzCurrentStorageAccount -Name premiumGen2Account -ResourceGroupName PremiumGen2Group

#Enable logging
Set-AzStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations read,write,delete -RetentionDays 14
```

### <a name="lifecycle-management-policies"></a>生命週期管理原則

- Premium 區塊 blob 儲存體帳戶尚未支援生命週期管理原則。 

- 無法將資料從進階層移至較低層。 

- 目前不支援「**刪除 Blob** 」動作。 

### <a name="hdinsight-support"></a>HDInsight 支援

當您建立 n 個 HDInsight 叢集時，您還無法選取已在其上啟用階層命名空間功能的區塊 blob 儲存體帳戶。 不過，您可以在建立叢集之後，將該帳戶附加至叢集。

### <a name="dremio-support"></a>Dremio 支援

Dremio 尚未連接到已啟用階層命名空間功能的區塊 blob 儲存體帳戶。 

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-data-lake-storage-gen2"></a>Windows Azure 儲存體 Blob （WASB）驅動程式（不支援 Data Lake Storage Gen2）

目前，WASB 驅動程式的設計僅適用于 Blob API，但在少數常見案例中遇到問題。 具體而言，當它是已啟用階層命名空間的儲存體帳戶的用戶端時。 Data Lake Storage 上的多重通訊協定存取不會減輕這些問題。 

在這段時間（而且很可能是可預見的未來），我們不支援使用 WASB 驅動程式做為已啟用階層命名空間之儲存體帳戶的用戶端。 相反地，我們建議您選擇使用 Hadoop 環境中的[Azure Blob 檔案系統（ABFS）](data-lake-storage-abfs-driver.md)驅動程式。 如果您嘗試使用的版本早于 Hadoop 分支-3 的內部部署 Hadoop 環境進行遷移，請開啟 Azure 支援票證，讓我們可以在您和貴組織的正確路徑中與您聯繫。
