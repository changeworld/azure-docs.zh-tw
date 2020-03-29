---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244599"
---
以下是可寫入的 Azure 物件大小。 請確定所有上傳的檔案均符合這些限制。

| Azure 物件類型 | 預設限制                                             |
|-------------------|-----------------------------------------------------------|
| 區塊 Blob        | ~ 4.75 TiB                                                 |
| 分頁 Blob         | 8 TiB <br> 以分頁 Blob 格式上傳的每個檔案都必須是 512 位元組規格 (整數倍數)，否則上傳會失敗。 <br> VHD 和 VHDX 為 512 位元組規格。 |
| Azure 檔案        | 1 TiB                                                      |
| 受控磁碟     | 4 TiB <br> 有關大小和限制的詳細資訊，請參閱： <li>[標準 SSD 的可伸縮性目標](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[高級 SSD 的可擴充性目標](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[標準硬碟的可伸縮性目標](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[託管磁片的定價和計費](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
