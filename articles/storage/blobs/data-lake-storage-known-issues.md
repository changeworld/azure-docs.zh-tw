---
title: Azure Data Lake Storage Gen2 的已知問題 | Microsoft Docs
description: 瞭解 Azure 數據存儲第 2 代的限制和已知問題。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.openlocfilehash: f69f17dc9d0cab2491a2c7f37b5bd082cc96b2d6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985417"
---
# <a name="known-issues-with-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 的已知問題

本文介紹了 Azure 數據存儲第 2 代的限制和已知問題。

## <a name="supported-blob-storage-features"></a>支援的 Blob 儲存體功能

越來越多的 Blob 儲存功能現在使用具有分層命名空間的帳戶。 關於完整清單,請參閱[Azure 資料湖儲存第 2 代 中可用的 Blob 儲存功能](data-lake-storage-supported-blob-storage-features.md)。

## <a name="supported-azure-service-integrations"></a>支援的 Azure 服務整合

Azure 資料儲存庫 Gen2 支援多個 Azure 服務,可用於引入資料、執行分析和創建視覺化表示。 有關受支援的 Azure 服務的清單,請參閱[支援 Azure 資料儲存湖儲存 Gen2 的 Azure 服務](data-lake-storage-supported-azure-services.md)。

請參閱[支援 Azure 資料儲存第 2 代](data-lake-storage-supported-azure-services.md)的 Azure 服務。

## <a name="supported-open-source-platforms"></a>支援的開放原始碼平台

數個開放原始碼平台支援 Data Lake Storage Gen2。 有關完整清單,請參閱[支援 Azure 資料儲存第 2 代 的開源平臺](data-lake-storage-supported-open-source-platforms.md)。

請參考[Azure 資料儲存第 2 代的開源平臺](data-lake-storage-supported-open-source-platforms.md)。

## <a name="blob-storage-apis"></a>Blob 儲存體 API

Blob API 和資料存儲第 2 代 API 可以對相同的資料進行操作。

本節介紹使用 blob API 和資料儲存存儲 Gen2 API 對同一數據進行操作的問題和限制。

* 不能同時使用 Blob API 和資料儲存 API 寫入檔案的同一實例。 如果使用 Data Lake 儲存 Gen2 API 寫入檔,則對[獲取阻止清單](https://docs.microsoft.com/rest/api/storageservices/get-block-list)blob API 的調用將看不到該檔案的塊。 您可以使用資料湖儲存 Gen2 API 或 Blob API 覆寫檔。 這不會影響文件屬性。

* 使用[清單 Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs)操作而不指定分隔符時,結果將同時包括目錄和 Blob。 如果選擇使用分隔符,則僅使用正向斜杠 ()。`/` 這是唯一受支援的分隔符。

* 如果使用[刪除 Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API 刪除目錄,則僅當該目錄為空時才會刪除該目錄。 這意味著您不能遞迴使用 Blob API 刪除目錄。

不支援這些 Blob REST API:

* [放置 Blob(頁)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [取得頁面範圍](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [漸寫 Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [從網址中放置頁面](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [放置 Blob(追加)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [網址中的附加區塊](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

具有分層命名空間的帳戶不支援非託管 VM 磁碟。 如果要在存儲帳戶上啟用分層命名空間,則將非託管 VM 磁碟放入未啟用分層命名空間功能的存儲帳戶中。

<a id="api-scope-data-lake-client-library" />

## <a name="file-system-support-in-sdks"></a>SDK 中的檔案系統支援

獲取和設置 ACL 操作當前不是遞迴的。

## <a name="file-system-support-in-powershell-and-azure-cli"></a>PowerShell 與 Azure CLI 中的檔案系統支援

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md)和[Azure CLI](data-lake-storage-directory-file-acl-cli.md)支援處於公共預覽版中。
- 獲取和設置 ACL 操作當前不是遞迴的。

## <a name="lifecycle-management-policies"></a>生命週期管理原則

* 尚不支援刪除 Blob 快照。  

## <a name="archive-tier"></a>存檔層

當前存在影響存檔存取層的錯誤。


## <a name="blobfuse"></a>Blobfuse

不支援 Blobfuse。

<a id="known-issues-tools" />

## <a name="azcopy"></a>AzCopy

僅使用最新版本的 AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json))。不支援早期版本的 AzCopy,如 AzCopy v8.1。

<a id="storage-explorer" />

## <a name="azure-storage-explorer"></a>Azure 儲存體總管

僅使用版本 `1.6.0` 或更高版本。

<a id="explorer-in-portal" />

## <a name="storage-explorer-in-the-azure-portal"></a>Azure 門戶的資源管理員

尚未支援 ACL。

<a id="third-party-apps" />

## <a name="thirdpartyapplications"></a>第三方申請

如果使用 REST API 工作的第三方應用程式,如果將其與調用 Blob API 的數據儲存儲存 Gen2 應用程式一起使用,則它們可能會正常工作。

## <a name="access-control-lists-acl-and-anonymous-read-access"></a>存取控制列表 (ACL) 與匿名讀取存取

如果已授予對容器的[匿名讀取訪問許可權](storage-manage-access-to-resources.md),則 ACL 對該容器或該容器中的檔沒有影響。

## <a name="windows-azure-storage-blob-wasb-driver-unsupported-with-adls-gen2"></a>Windows Azure 儲存 Blob (WASB) 驅動程式(ADLS Gen2 不支援)

目前,WASB 驅動程式(設計為僅使用 Blob API)在一些常見方案中遇到問題,即當它是啟用命名空間的存儲帳戶的用戶端時。 請注意,多協定訪問 (MPA) 也不會緩解這些問題。 

目前(而且很可能是可預見的未來),我們不會支援使用 WASB 驅動程式作為用戶端的客戶訪問啟用命名空間的存儲帳戶。 相反,我們建議您選擇在 Hadoop 環境中使用[Azure Blob 檔案系統 (ABFS)](data-lake-storage-abfs-driver.md)驅動程式。 如果您嘗試移轉出具有早於 Hadoop 分支 3 版本的本地 Hadoop 環境,請打開 Azure 支援票證,以便我們可以在您和您的組織前進的正確路徑上與您聯繫。
