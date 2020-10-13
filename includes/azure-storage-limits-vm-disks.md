---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: e89676505bc211d01a4327f8816a048218e579fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91829061"
---
您可以將一些資料磁碟連結至 Azure 虛擬機器。 根據 VM 資料磁碟的可擴縮性和效能目標，您可以決定您需要的磁碟數目和類型，以符合您的效能和容量需求。

> [!IMPORTANT]
> 為達最佳效能，限制連結至虛擬機器的高度使用磁碟數目，以避免可能的節流。 如果所有連結的磁碟並未同時高度使用，則虛擬機器可支援更多磁碟。

**針對 Azure 受控磁碟：**

下表說明每個訂用帳戶每個區域的預設資源數和資源數上限。 每個資源群組的受控磁碟、快照集和映像數目沒有限制。  

> | 資源 | 限制 |
> | --- | --- |
> | 標準受控磁碟 | 50,000 |
> | 標準 SSD 受控磁碟 | 50,000 |
> | 進階受控磁碟 | 50,000 |
> | Standard_LRS 快照集 | 50,000 |
> | Standard_ZRS 快照集 | 50,000 |
> | 受控映像 | 50,000 |

**若為標準儲存體帳戶：** 標準儲存體帳戶的總要求率上限為 20000 IOPS。 在標準儲存體帳戶中，所有虛擬機器磁碟的 IOPS 總數不得超過此限。
  
    You can roughly calculate the number of highly utilized disks supported by a single Standard storage account based on the request rate limit. For example, for a Basic tier VM, the maximum number of highly utilized disks is about 66, which is 20,000/300 IOPS per disk. The maximum number of highly utilized disks for a Standard tier VM is about 40, which is 20,000/500 IOPS per disk. 

**若為進階儲存體帳戶：** 進階儲存體帳戶的總輸送量速率上限為 50 Gbps。 所有 VM 磁碟的總輸送量不應該超過此限。

