---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334750"
---
您可以將許多資料磁片附加到 Azure 虛擬機器。 根據 VM 資料磁片的可伸縮性和性能目標，您可以確定滿足性能和容量要求所需的磁片數量和類型。

> [!IMPORTANT]
> 為達最佳效能，限制連結至虛擬機器的高度使用磁碟數目，以避免可能的節流。 如果同時未充分利用所有連接的磁片，虛擬機器可以支援更多磁片。

**對於 Azure 託管磁片：**

下表說明了每個訂閱每個區域的資源數的預設和最大限制。 每個資源組的託管磁片、快照和映射數沒有限制。  

> | 資源 | 限制 |
> | --- | --- |
> | 標準受控磁碟 | 50,000 |
> | 標準 SSD 託管磁片 | 50,000 |
> | 高級託管磁片 | 50,000 |
> | Standard_LRS快照 | 50,000 |
> | Standard_ZRS快照 | 50,000 |
> | 託管映射 | 50,000 |

* **對於標準存儲帳戶：** 標準存儲帳戶的最大總請求率為 20，000 IOPS。 標準存儲帳戶中所有虛擬機器磁片的總 IOPS 不應超過此限制。
  
    您可以根據請求速率限制粗略計算單個標準存儲帳戶支援的高利用率磁片數。 例如，對於基本層 VM，高利用率磁片的最大數量約為 66，即每個磁片 20，000/300 IOPS。 標準層 VM 的最大高度使用磁片數約為 40，即每個磁片 20，000/500 IOPS。 

* **對於高級存儲帳戶：** 高級存儲帳戶的最大總吞吐率為 50 Gbps。 所有 VM 磁碟的總輸送量不應該超過此限。

