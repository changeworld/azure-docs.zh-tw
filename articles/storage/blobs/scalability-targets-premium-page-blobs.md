---
title: 高級頁面 Blob 存儲帳戶的可伸縮性目標
titleSuffix: Azure Storage
description: 高級性能頁 blob 存儲帳戶針對讀/寫操作進行了優化。 這種類型的存儲帳戶支援 Azure 虛擬機器的非託管磁片。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: e134c69f5d602cb4369e9410e3e2b9d3478b11a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756244"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>高級頁面 Blob 存儲帳戶的可擴充性和性能目標

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>擴展高級頁面 blob 帳戶的目標

針對讀/寫操作優化了高性能頁面 Blob 存儲帳戶。 這種類型的存儲帳戶支援 Azure 虛擬機器的非託管磁片。

> [!NOTE]
> 如果可能，Microsoft 建議將託管磁片與 Azure 虛擬機器 （VM） 一起使用。 有關託管磁片的詳細資訊，請參閱 Windows [VM 的 Azure 磁片存儲概述](../../virtual-machines/windows/managed-disks-overview.md)。

高級頁面 Blob 存儲帳戶具有以下可伸縮性目標：

| 總帳戶容量                            | 本地備援儲存體帳戶總頻寬                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 磁片容量：4 TB（單個磁片）/35 TB（所有磁片的累計總數） <br>快照容量：10 TB | 每秒最多 50 GB (輸入 <sup>1</sup> + 輸出 <sup>2</sup> |

<sup>1</sup> 傳送至儲存體帳戶的所有資料 (要求)

<sup>2</sup> 從儲存體帳戶接收的所有資料 (回應)

高級頁面 blob 帳戶是配置為高級性能的通用帳戶。 建議使用通用 v2 存儲帳戶。

如果您對非託管磁片使用高級頁面 Blob 存儲帳戶，並且應用程式超出了單個存儲帳戶的可伸縮性目標，則 Microsoft 建議遷移到託管磁片。 有關託管磁片的詳細資訊，請參閱 Windows [VM 的 Azure 磁片存儲概述](../../virtual-machines/windows/managed-disks-overview.md)或[Linux VM 的 Azure 磁片存儲概述](../../virtual-machines/linux/managed-disks-overview.md)。

如果無法遷移到託管磁片，則生成應用程式以使用多個存儲帳戶，並在這些存儲帳戶中分區資料。 例如，如果您想要將 51 TB 的磁碟連結至多個 VM，可將其分散到兩個儲存體帳戶。 單一進階儲存體帳戶的限制是 35 TB。 確保單個高級性能存儲帳戶的預配磁片數不超過 35 TB。

## <a name="see-also"></a>另請參閱

- [標準儲存體帳戶的延展性和效能目標](../common/scalability-targets-standard-account.md)
- [高級塊 Blob 存儲帳戶的可伸縮性目標](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure 訂用帳戶限制與配額](../../azure-resource-manager/management/azure-subscription-service-limits.md)
