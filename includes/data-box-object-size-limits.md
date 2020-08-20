---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 9805f53d5901226fc9e32b24a323256cd1da6844
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88655237"
---
以下是可寫入的 Azure 物件大小。 請確定所有上傳的檔案均符合這些限制。

| Azure 物件類型 | 預設限制                                             |
|-------------------|-----------------------------------------------------------|
| 區塊 Blob        | ~ 4.75 TiB                                                 |
| 分頁 Blob         | 8 TiB <br> 以分頁 Blob 格式上傳的每個檔案都必須是 512 位元組規格 (整數倍數)，否則上傳會失敗。 <br> VHD 和 VHDX 為 512 位元組規格。 |
| Azure 檔案        | 1 TiB                                                      |
| 受控磁碟     | 4 TiB <br> 如需大小和限制的詳細資訊，請參閱： <li>[標準 Ssd 的擴充性目標](../articles/virtual-machines/disks-types.md#standard-ssd)</li><li>[Premium Ssd 的擴充性目標](../articles/virtual-machines/disks-types.md#standard-hdd)</li><li>[標準 Hdd 的擴充性目標](../articles/virtual-machines/disks-types.md#premium-ssd)</li><li>[受控磁片的定價和計費](../articles/virtual-machines/disks-types.md#billing)</li>  
