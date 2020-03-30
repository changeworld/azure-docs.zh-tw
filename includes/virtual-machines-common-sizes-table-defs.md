---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 63c53a9b95e27486d7d6944c28f8fb085b1bc6ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74279185"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>資料表大小定義

- 儲存容量會以 GiB 或是 1024^3 位元組為單位顯示。 當您將以 GB （1000×3 位元組） 為單位測量的磁片與以 GiB （1024⁄3） 為單位測量的磁片進行比較時，請記住，GiB 中給出的容量號可能顯得較小。 例如，1023 GiB = 1098.4 GB。
- 磁碟輸送量是以每秒輸入/輸出作業 (IOPS) 和 MBps 進行測量，其中 MBps = 10^6 位元組/每秒。
- 資料磁碟可以在快取模式或取消快取模式下運作。 針對快取的資料磁碟作業，主機快取模式必須設定為 **ReadOnly** 或 **ReadWrite**。  針對取消快取的資料磁碟作業，主機快取模式必須設定為 **None**。
- 如果要為 VM 獲得最佳性能，則應將資料磁片數限制為每個 vCPU 兩個磁片。
- **預期的網路頻寬**是根據跨所有目的地之所有 NIC 的 VM 類型所配置的最大彙總頻寬。 有關詳細資訊，請參閱[虛擬機器網路頻寬](../articles/virtual-network/virtual-machine-network-throughput.md)。

  上限不保證。 限制為為預期應用程式選擇正確的 VM 類型提供了指導。 實際網路性能將取決於幾個因素，包括網路擁塞、應用程式負載和網路設置。 有關優化網路輸送量的資訊，請參閱優化[Azure 虛擬機器的網路輸送量](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 要在 Linux 或 Windows 上實現預期的網路性能，您可能需要選擇特定版本或優化 VM。 有關詳細資訊，請參閱[頻寬/輸送量測試 （NTTTCP）。](../articles/virtual-network/virtual-network-bandwidth-testing.md)



