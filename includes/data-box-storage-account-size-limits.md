---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 01/12/2021
ms.author: alkohli
ms.openlocfilehash: 6cabac4c59b09d146c1e42762402043622edb60e
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225392"
---
以下是複製到儲存體帳戶的資料大小限制。 請確定您上傳的資料符合這些限制。 如需這些限制的最新資訊，請參閱 [Blob 儲存體的擴充性和效能目標](../articles/storage/blobs/scalability-targets.md) ，以及 Azure 檔案儲存體的擴充 [性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 最大限制與 [針對 Azure 訂用帳戶定義的儲存體限制](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits) 相同，而且它包含來自所有來源（包括資料箱）的資料。   |
| Azure 檔案                                                          | 資料箱支援大型檔案共用 (100 TiB) 如果在建立資料箱訂單之前啟用的話）。 <br> 如果在建立訂單之前未啟用，支援的檔案共用大小上限為 5 TiB。 <br> 資料箱支援 Azure Premium 檔案共用，可針對儲存體帳戶中的所有共用允許總共 100 TiB。 <br> 由於複製記錄檔和 audit 記錄檔所使用的空間，最大可用容量會稍微減少。 每個最小 100 GiB 會保留給複製記錄檔和審核記錄。 如需詳細資訊，請參閱 [Azure 資料箱的 Audit 記錄檔 Azure Data Box Heavy](../articles/databox/data-box-audit-logs.md)。 <br> StorageAccount_AzureFiles 底下的所有資料夾必須遵循此限制。 <br> 如需詳細資訊，請參閱 [啟用和建立大型檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md)      |
