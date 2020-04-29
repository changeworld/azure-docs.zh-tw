---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75469272"
---
以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 如需有關這些限制的最新資訊，請參閱[Blob 儲存體的擴充性和效能目標](../articles/storage/blobs/scalability-targets.md)和 Azure 檔案儲存體的擴充[性和效能目標](../articles/storage/files/storage-files-scale-targets.md)。

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 2 PB，適用于美國和歐洲。<br>適用于所有其他區域的 500 TB，包括英國。  <br> 其中包含來自所有來源 (包括資料箱) 的資料。|
| Azure 檔案                                                          | 每個共用 5 TB。<br> StorageAccount_AzureFiles** 底下的所有資料夾必須遵循此限制。       |
