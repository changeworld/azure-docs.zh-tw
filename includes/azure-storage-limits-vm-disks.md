---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: c3028ed7629c41eece354dd2554ede9249bac4f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80334750"
---
您可以將數個數據磁片連接到 Azure 虛擬機器。 根據 VM 資料磁片的擴充性和效能目標，您可以決定您需要的磁片數目和類型，以符合您的效能和容量需求。

> [!IMPORTANT]
> 為達最佳效能，限制連結至虛擬機器的高度使用磁碟數目，以避免可能的節流。 如果所有連接的磁片不會同時高度使用，則虛擬機器可以支援更多的磁片。

**針對 Azure 受控磁片：**

下表說明每個訂用帳戶每個區域的預設和最大資源數限制。 每個資源群組的受控磁碟、快照集和映射數目沒有限制。  

> | 資源 | 限制 |
> | --- | --- |
> | 標準受控磁碟 | 50,000 |
> | 標準 SSD 受控磁片 | 50,000 |
> | Premium 受控磁片 | 50,000 |
> | Standard_LRS 快照集 | 50,000 |
> | Standard_ZRS 快照集 | 50,000 |
> | 受控映射 | 50,000 |

* **針對標準儲存體帳戶：** 標準儲存體帳戶的總要求率上限為 20000 IOPS。 標準儲存體帳戶中所有虛擬機器磁片的 IOPS 總計不應超過此限制。
  
    您可以根據要求速率限制，大致計算單一標準儲存體帳戶所支援的高度使用磁片數目。 例如，針對基本層 VM，高度使用的磁片數目上限大約是66，也就是每一磁片 20000/300 IOPS。 標準層 VM 的高度使用磁片數目上限大約是40，也就是每一磁片 20000/500 IOPS。 

* **Premium 儲存體帳戶：** Premium 儲存體帳戶的總輸送量速率上限為 50 Gbps。 所有 VM 磁碟的總輸送量不應該超過此限。

