---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/04/2019
ms.author: alkohli
ms.openlocfilehash: 563849d3875ed0156d81770f58340633d90d515b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174432"
---
以下是可寫入的 Azure 物件大小。 請確定所有上傳的檔案均符合這些限制。

| Azure 物件類型 | 上傳限制                                             |
|-------------------|-----------------------------------------------------------|
| 區塊 Blob        | ~ 4.75 TB                                                 |
| 分頁 Blob         | 1 TB <br> 以分頁 Blob 格式上傳的每個檔案都必須是 512 位元組規格 (整數倍數)，否則上傳會失敗。 <br> VHD 和 VHDX 為 512 位元組規格。 |
| Azure 檔案         | 1 TB <br> 以分頁 Blob 格式上傳的每個檔案都必須是 512 位元組規格 (整數倍數)，否則上傳會失敗。 <br> VHD 和 VHDX 為 512 位元組規格。 |

> [!IMPORTANT]
> 允許建立最多 5 TB 的檔案 (無論儲存體類型)。 不過，如果您建立的檔案大小超過上表定義的上傳限制時，則無法上傳檔案。 您必須手動刪除檔案以回收空間。