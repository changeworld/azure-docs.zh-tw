---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 3e1f9c225a57e7d41f85c2a92dac989453057c51
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736972"
---
以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需有關這些限制的最新資訊，請參閱[Blob 儲存體的擴充性和效能目標](../articles/storage/blobs/scalability-targets.md)和 Azure 檔案儲存體的擴充[性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 2 PB，適用于美國和歐洲。<br>適用于所有其他區域的 500 TB，包括英國。  <br> 其中包含來自所有來源 (包括資料箱) 的資料。|
| Azure 檔案                                                          | 標準檔案共用的大小上限 100TiB *、5 TB、每個共用100TiB 的 Premium 檔案共用。<br> StorageAccount_AzureFiles** 底下的所有資料夾必須遵循此限制。       |
