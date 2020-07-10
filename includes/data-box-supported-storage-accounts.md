---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200304"
---
以下是資料箱裝置所支援的儲存體帳戶和儲存體類型清單。 如需所有不同儲存體帳戶類型和其全部功能的完整清單，請參閱[儲存體帳戶的類型](/azure/storage/common/storage-account-overview#types-of-storage-accounts)。

針對 [匯入訂單]，下表顯示支援的儲存體帳戶。

| **儲存體帳戶/支援的儲存體類型** | **區塊 Blob** |**分頁 Blob*** |**Azure 檔案** |**注意事項**|
| --- | --- | -- | -- | -- |
| 傳統標準 | Y | Y | 是 |
| 一般用途 v1 標準  | Y | Y | 是 | 經常性存取和非經常性存取均受支援。|
| 一般用途 v1 進階  |  | 是| | |
| 一般用途 v2 標準  | Y | Y | 是 | 經常性存取和非經常性存取均受支援。|
| 一般用途 v2 進階  |  |Y | | |
| Blob 儲存體標準 |Y | | |經常性存取和非經常性存取均受支援。 |

\* *- 上傳至分頁 Blob 的資料必須是 512 位元組規格，例如 VHD。*

針對匯出訂單，下表顯示支援的儲存體帳戶。

| **儲存體帳戶/支援的儲存體類型** | **區塊 Blob** |**分頁 Blob*** |**Azure 檔案** |**支援的存取層**|
| --- | --- | -- | -- | -- |
| 傳統標準 | 是 | 是 | 是 | |
| 一般用途 v1 標準  | 是 | 是 | 是 | 經常性、非經常性|
| 一般用途 v1 進階  |  | 是| | |
| 一般用途 v2 標準  | 是 | 是 | 是 | 經常性、非經常性|
| 一般用途 v2 進階  |  |Y | | |
| Blob 儲存體標準 |Y | | |經常性、非經常性 |
| 區塊 Blob 儲存體 Premium |是 | | |經常性、非經常性 |
| 分頁 Blob 儲存體 Premium | |是 | | |

> [!IMPORTANT]
> - 針對一般用途的帳戶，資料箱不支援佇列、資料表、磁片和 Azure Data Lake Gen 2 儲存體類型。
> - 資料箱不支援 Blob 儲存體和封鎖 Blob 儲存體帳戶的附加 blob。
> - 資料箱不支援 Premium 檔案儲存體帳戶。
> - 上傳至分頁 blob 的資料必須對齊512個位元組，例如 Vhd。
> - 最多可以匯出 80 TB。
> - 檔案歷程記錄和 blob 快照集不會匯出。


