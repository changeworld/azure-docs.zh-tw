---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: bc156b8c18f46cccf6fc775b82f76383b8c43861
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "66242182"
---
以下是資料箱裝置所支援的儲存體帳戶和儲存體類型清單。 如需所有不同儲存體帳戶類型和其全部功能的完整清單，請參閱[儲存體帳戶的類型](/azure/storage/common/storage-account-overview#types-of-storage-accounts)。

| **儲存體帳戶/支援的儲存體類型** | **區塊 Blob** |**頁面 blob*** |**Azure 檔** |**注意**|
| --- | --- | -- | -- | -- |
| 傳統標準 | Y | Y | Y |
| 一般用途 v1 標準  | Y | Y | Y | 經常性存取和非經常性存取均受支援。|
| 一般用途 v1 進階  |  | Y| | |
| 一般用途 v2 標準  | Y | Y | Y | 經常性存取和非經常性存取均受支援。|
| 一般用途 v2 進階  |  |Y | | |
| Blob 儲存體標準 |Y | | |經常性存取和非經常性存取均受支援。 |

\**- 上載到頁面 blob 的資料必須為 512 個位元組對齊，如 VHD。*

>[!NOTE]
> 不支援 Azure Data Lake Storage Gen 2 帳戶。
