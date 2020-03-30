---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2303d36e93cecfca03894a8b0e55458c03b13d78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73412949"
---
**輸出資料傳輸**： [輸出資料傳輸](https://azure.microsoft.com/pricing/details/bandwidth/) (Azure 資料中心送出的資料) 會產生頻寬使用量費用。

**事務**：根據在標準託管磁片上執行的事務數計費。 對於標準 SSD，每個 I/O 操作小於或等於 256 KiB 的輸送量被視為單個 I/O 操作。 大於 256 KiB 輸送量的 I/O 操作被視為尺寸為 256 KiB 的多個 I/O。 對於標準 HDD，無論 I/O 大小如何，每個 IO 操作都被視為單個事務。

有關託管磁片定價的詳細資訊（包括事務成本），請參閱[託管磁片定價](https://azure.microsoft.com/pricing/details/managed-disks)。

### <a name="ultra-disk-vm-reservation-fee"></a>超磁片 VM 預訂費

Azure VM 能夠指示它們是否與超級磁片相容。 與 Ultra 磁碟相容的 VM 會在計算 VM 執行個體與區塊儲存體的縮放單位之間配置專用頻寬容量，以最佳化效能並減少延遲。 若在 VM 上新增此功能，只有在未將 Ultra 磁碟連結至該 VM 的情況下於 VM 上啟用 Ultra 磁碟功能時，才會產生保留費用。 當 Ultra 磁碟連結至與 Ultra 磁碟相容的 VM 時，就不會收取此費用。 此費用會根據在 VM 上佈建的 vCPU 計費。 

> [!Note]
> 對於[受限的核心 VM 大小](../articles/virtual-machines/linux/constrained-vcpu.md)，保留費用基於實際 vCPU 數，而不是受限內核。 對於Standard_E328s_v3，預訂費將基於32個核心。 

有關超級磁片定價的詳細資訊，請參閱[Azure 磁片定價頁](https://azure.microsoft.com/pricing/details/managed-disks/)。

### <a name="azure-disk-reservation"></a>Azure 磁片預留

磁片預留是提前購買一年的磁片存儲以折扣的選項，從而降低了您的總成本。 購買磁片預留時，您可以在目的地區域中選擇特定的磁片 SKU，例如，在美國東部 2 區域選擇 10 P30 （1TiB） 高級 SSD，期限為一年。 預留體驗類似于預留虛擬機器 （VM） 實例。 您可以捆綁 VM 和磁片預留，以最大限度地節省成本。 目前，Azure 磁片預留為所有生產區域的優質 SSD SKU 提供了一年承諾計畫，從 P30 （1TiB） 到 P80 （32 TiB）。 有關預留磁片定價的更多詳細資訊，請參閱 Azure[磁片定價頁](https://azure.microsoft.com/pricing/details/managed-disks/)。