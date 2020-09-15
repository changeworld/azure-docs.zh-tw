---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 91f91b1260cc445f90c2608fc5259ad61acd37ac
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533341"
---
以下是資料箱裝置所支援的儲存體帳戶和儲存體類型清單。 如需所有不同儲存體帳戶類型和其全部功能的完整清單，請參閱[儲存體帳戶的類型](/azure/storage/common/storage-account-overview#types-of-storage-accounts)。

針對匯入順序，下表顯示支援的儲存體帳戶。

| **儲存體帳戶/支援的儲存體類型** | **區塊 Blob** |**分頁 Blob*** |**Azure 檔案** |**注意事項**|
| --- | --- | -- | -- | -- |
| 傳統標準 | Y | Y | Y |
| 一般用途 v1 標準  | Y | Y | Y | 經常性存取和非經常性存取均受支援。|
| 一般用途 v1 進階  |  | Y| | |
| 一般用途 v2 標準  | Y | Y | Y | 經常性存取和非經常性存取均受支援。|
| 一般用途 v2 進階  |  |Y | | |
| Blob 儲存體標準 |Y | | |經常性存取和非經常性存取均受支援。 |

\* *- 上傳至分頁 Blob 的資料必須是 512 位元組規格，例如 VHD。*

針對匯出訂單，下表顯示支援的儲存體帳戶。

| **儲存體帳戶/支援的儲存體類型** | **區塊 Blob** |**分頁 Blob*** |**Azure 檔案** |**支援的存取層**|
| --- | --- | -- | -- | -- |
| 傳統標準 | Y | Y | Y | |
| 一般用途 v1 標準  | Y | Y | Y | 經常性、非經常性|
| 一般用途 v1 進階  |  | Y| | |
| 一般用途 v2 標準  | Y | Y | Y | 經常性、非經常性|
| 一般用途 v2 進階  |  |Y | | |
| Blob 儲存體標準 |Y | | |經常性、非經常性 |
| 區塊 Blob 儲存體 Premium |Y | | |經常性、非經常性 |
| 分頁 Blob 儲存體 Premium | |Y | | |

> [!IMPORTANT]
> - 針對一般用途的帳戶，資料箱不支援匯入順序的佇列、資料表和磁片儲存體類型。 針對匯出訂單，資料箱不支援一般用途帳戶的佇列、資料表、磁片和 Azure Data Lake Gen 2 儲存體類型。
> - 資料箱不支援針對 Blob 儲存體和區塊 Blob 儲存體帳戶附加 blob。
> - 資料箱不支援 Premium 檔案儲存體帳戶。
> - 上傳至分頁 blob 的資料必須為512個位元組，例如 Vhd。
> - 最多可以匯出 80 TB。
> - 檔案歷程記錄和 blob 快照集不會匯出。


