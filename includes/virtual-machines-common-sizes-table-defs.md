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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74279185"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>資料表大小定義

- 儲存容量會以 GiB 或是 1024^3 位元組為單位顯示。 當您比較以 GB （1000 ^ 3 位元組）測量的磁片和以 GiB （1024 ^ 3）測量的磁片時，請記住，GiB 中提供的容量號碼可能會較小。 例如，1023 GiB = 1098.4 GB。
- 磁碟輸送量是以每秒輸入/輸出作業 (IOPS) 和 MBps 進行測量，其中 MBps = 10^6 位元組/每秒。
- 資料磁碟可以在快取模式或取消快取模式下運作。 針對快取的資料磁碟作業，主機快取模式必須設定為 **ReadOnly** 或 **ReadWrite**。  針對取消快取的資料磁碟作業，主機快取模式必須設定為 **None**。
- 如果您想要取得 Vm 的最佳效能，您應該將每個 vCPU 的資料磁片數目限制為兩個磁片。
- **預期的網路頻寬**是根據跨所有目的地之所有 NIC 的 VM 類型所配置的最大彙總頻寬。 如需詳細資訊，請參閱[虛擬機器網路頻寬](../articles/virtual-network/virtual-machine-network-throughput.md)。

  不保證上限。 限制提供針對預期的應用程式選取正確 VM 類型的指引。 實際的網路效能將取決於數個因素，包括網路擁塞、應用程式負載和網路設定。 如需優化網路輸送量的詳細資訊，請參閱將[Azure 虛擬機器的網路輸送量優化](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md)。 若要在 Linux 或 Windows 上達到預期的網路效能，您可能需要選取特定版本或優化您的 VM。 如需詳細資訊，請參閱[頻寬/輸送量測試（NTTTCP）](../articles/virtual-network/virtual-network-bandwidth-testing.md)。



