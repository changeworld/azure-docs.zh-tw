---
title: Premium 頁面 blob 儲存體帳戶的擴充性目標
titleSuffix: Azure Storage
description: Premium 效能頁面 blob 儲存體帳戶已針對讀取/寫入作業優化。 這種類型的儲存體帳戶可支援 Azure 虛擬機器的非受控磁片。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 23a176bfa5e3861dbc4ad5c03ea54fc847d3f56b
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96922534"
---
# <a name="scalability-and-performance-targets-for-premium-page-blob-storage-accounts"></a>Premium 頁面 blob 儲存體帳戶的擴充性和效能目標

[!INCLUDE [storage-scalability-intro-include](../../../includes/storage-scalability-intro-include.md)]

## <a name="scale-targets-for-premium-page-blob-accounts"></a>Premium 頁面 blob 帳戶的調整目標

Premium 效能頁面 blob 儲存體帳戶已針對讀取/寫入作業優化。 這種類型的儲存體帳戶可支援 Azure 虛擬機器的非受控磁片。

> [!NOTE]
> Microsoft 建議您盡可能將受控磁片與 Azure 虛擬機器搭配使用 (Vm) 。 如需受控磁片的詳細資訊，請參閱 [vm Azure 磁碟儲存體總覽](../../virtual-machines/managed-disks-overview.md)。

Premium 頁面 blob 儲存體帳戶具有下列擴充性目標：

| 總帳戶容量                            | 本地備援儲存體帳戶總頻寬                     |
| ------------------------------------------------- | --------------------------------------------------------------------------- |
| 磁片容量： 4 TB (個別磁片) /35 TB (所有磁片的累計總計)  <br>快照容量：10 TB | 每秒最多 50 GB (輸入 <sup>1</sup> + 輸出 <sup>2</sup> |

<sup>1</sup> 傳送至儲存體帳戶的所有資料 (要求)

<sup>2</sup> 從儲存體帳戶接收的所有資料 (回應)

Premium 頁面 blob 帳戶是針對 premium 效能設定的一般用途帳戶。 建議使用一般用途 v2 儲存體帳戶。

如果您針對非受控磁片使用 premium 頁面 blob 儲存體帳戶，且您的應用程式超過單一儲存體帳戶的擴充性目標，則 Microsoft 建議您遷移至受控磁片。 如需受控磁片的詳細資訊，請參閱 [vm Azure 磁碟儲存體總覽](../../virtual-machines/managed-disks-overview.md)。

如果您無法遷移至受控磁片，請建立您的應用程式以使用多個儲存體帳戶，並在這些儲存體帳戶之間分割資料。 例如，如果您想要將 51 TB 的磁碟連結至多個 VM，可將其分散到兩個儲存體帳戶。 單一進階儲存體帳戶的限制是 35 TB。 請確定單一 premium 效能儲存體帳戶永遠不會有超過 35 TB 的布建磁片。

## <a name="see-also"></a>另請參閱

- [標準儲存體帳戶的延展性和效能目標](../common/scalability-targets-standard-account.md)
- [Premium 區塊 blob 儲存體帳戶的擴充性目標](../blobs/scalability-targets-premium-block-blobs.md)
- [Azure 訂用帳戶限制與配額](../../azure-resource-manager/management/azure-subscription-service-limits.md)
