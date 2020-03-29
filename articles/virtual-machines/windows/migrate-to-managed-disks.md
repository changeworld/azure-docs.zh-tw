---
title: 將 Azure VM 遷移到託管磁片
description: 將使用儲存體帳戶中非受控磁碟建立的 Azure 虛擬機器移轉成使用受控磁碟。
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e8f2753ac9062803a2d6252eca1829cb0b168f02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921346"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>將 Azure VM 移轉至 Azure 中的受控磁碟

Azure 受控磁碟可免除個別管理儲存體帳戶的需求，進而簡化儲存體管理。  您也可以將現有的 Azure VM 移轉至受控磁碟，以便受惠於可用性設定組中更佳的 VM 可靠性。 它可確保可用性集中不同 VM 的磁片彼此足夠隔離，以避免單點故障。 它會自動以不同的儲存體縮放單位 (戳記) 將不同 VM 的磁碟放在一個可用性設定組中，以限制硬體和軟體失敗所引起之單一儲存體縮放單位失敗的影響。
根據您的需求，您可從四種類型的儲存體選項中選擇。 要瞭解可用的磁片類型，請參閱我們的文章["選擇磁片類型](disks-types.md)"

## <a name="migration-scenarios"></a>移轉案例

您可以在下列案例中移轉到受控磁碟︰

|狀況  |發行項  |
|---------|---------|
|將獨立 VM 和可用性設定組中的 VM 轉換成受控磁碟     |[將 VM 轉換成使用受控磁碟](convert-unmanaged-to-managed-disks.md)         |
|將單個 VM 從經典 VM 轉換為託管磁片上的資源管理器     |[從傳統 VHD 建立 VM](create-vm-specialized-portal.md)         |
|將 vNet 中的所有 VM 從經典轉換為託管磁片上的資源管理器     |[將 IaaS 資源從傳統部署移轉至 Resource Manager 部署](migration-classic-resource-manager-ps.md)，然後[將 VM 從非受控磁碟轉換為受控磁碟](convert-unmanaged-to-managed-disks.md)         |
|使用標準非託管磁片將 VM 升級到具有託管高級磁片的 VM     | 首先，[將 Windows 虛擬機器從非託管磁片轉換為託管磁片](convert-unmanaged-to-managed-disks.md)。 然後[更新託管磁片的存儲類型](convert-disk-storage.md)。         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>後續步驟

- 瞭解有關[託管磁片](managed-disks-overview.md)的更多
- 請檢閱[受控磁碟的價格](https://azure.microsoft.com/pricing/details/managed-disks/)。
