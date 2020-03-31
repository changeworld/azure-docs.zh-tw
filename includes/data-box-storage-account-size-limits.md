---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 06/18/2019
ms.author: alkohli
ms.openlocfilehash: 6a9c31eb46a457433834d5940b3f7e190ebe1476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75469272"
---
以下是複製到儲存體帳戶中的資料在大小方面的限制。 請確定您上傳的資料符合這些限制。 有關這些限制的最新資訊，請參閱 Blob 存儲和 Azure[檔的可伸縮性和性能目標](../articles/storage/blobs/scalability-targets.md)。 [Azure Files scalability and performance targets](../articles/storage/files/storage-files-scale-targets.md)

| 複製到 Azure 儲存體帳戶中的資料大小                      | 預設限制          |
|---------------------------------------------------------------------|------------------------|
| 區塊 Blob 和分頁 Blob                                            | 2 PB，適用于美國和歐洲。<br>500 TB 適用于所有其他地區，包括英國。  <br> 其中包含來自所有來源 (包括資料箱) 的資料。|
| Azure 檔案                                                          | 每股 5 TB。<br> StorageAccount_AzureFiles** 底下的所有資料夾必須遵循此限制。       |
