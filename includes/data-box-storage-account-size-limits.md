---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/24/2020
ms.author: alkohli
ms.openlocfilehash: 2868e5a53686cfa94dc206c1aab65fe866d19b6d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028570"
---
以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需這些限制的最新資訊，請參閱 [Blob 儲存體的擴充性和效能目標](../articles/storage/blobs/scalability-targets.md) ，以及 Azure 檔案儲存體的擴充 [性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 最大限制與 [針對 Azure 訂用帳戶定義的儲存體限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 相同，而且它包含來自所有來源（包括資料箱）的資料。 |
| Azure 檔案                                                          | 資料箱支援大型檔案共用 (100TiB) 如果在建立資料箱訂單之前啟用的話）。 <br> 如果在建立訂單之前未啟用，支援的檔案共用大小上限為 5 TiB。 <br> 尚未支援 Premium 檔案共用。<br> StorageAccount_AzureFiles 底下的所有資料夾必須遵循此限制。 <br> 如需詳細資訊，請參閱 [啟用和建立大型檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |