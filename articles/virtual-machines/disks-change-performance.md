---
title: Azure 受控磁片的效能層級
description: 瞭解受控磁片的效能層級。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/19/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 28980756ac9e41c9477d687ea9df608b512759e3
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/19/2020
ms.locfileid: "94986773"
---
# <a name="performance-tiers-for-managed-disks"></a>受控磁片的效能層級

當您建立磁片時，會以其效能層的形式設定 Azure 受控磁片的效能。 效能層級會決定受控磁片的 IOPS 和輸送量。 當您設定磁片的布建大小時，系統會自動選取效能層級。 您可以在部署或之後變更效能層級，而不需要變更磁片的大小。

變更效能層級可讓您準備並符合較高的需求，而不需要使用磁片的高載功能。 視需要額外的效能，您可以更符合成本效益的方式變更效能層級，而不是依賴高載。 這適用于暫時需要一致效能等級的事件，例如假日購物、效能測試或執行定型環境。 若要處理這些事件，只要需要，您可以使用較高的效能層級。 當您不再需要額外的效能時，您可以返回原始層。

## <a name="how-it-works"></a>運作方式

當您第一次部署或布建磁片時，該磁片的基準效能層級會根據布建的磁片大小來設定。 您可以使用高於原始基準的效能層級來滿足更高的需求。 當您不再需要該效能層級時，您可以回到初始基準效能層級。

當您的效能層級變更時，您的帳單就會變更。 例如，如果您布建 P10 磁片 (128 GiB) ，您的基準效能層級會設定為 P10 (500 IOPS 和 100 MBps) 。 您將以 P10 費率計費。 您可以升級層級，以符合 P50 (7500 IOPS 和 250 MBps) 的效能，而不會增加磁片大小。 在升級期間，您將以 P50 費率計費。 當您不再需要較高的效能時，您可以回到 P10 層。 磁片會再次以 P10 費率計費。

| 磁碟大小 | 基準效能層級 | 可以升級為 |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2、P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 8 GiB | P2 | P3、P4、P6、P10、P15、P20、P30、P40、P50 |
| 16 GiB | P3 | P4、P6、P10、P15、P20、P30、P40、P50 | 
| 32 GiB | P4 | P6、P10、P15、P20、P30、P40、P50 |
| 64 GiB | P6 | P10、P15、P20、P30、P40、P50 |
| 128 GiB | P10 | P15、P20、P30、P40、P50 |
| 256 GiB | P15 | P20、P30、P40、P50 |
| 512 GiB | P20 | P30、P40、P50 |
| 1 TiB | P30 | P40、P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | None |
| 8 TiB | P60 |  P70、P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | None |

如需帳單資訊，請參閱 [受控磁片定價](https://azure.microsoft.com/pricing/details/managed-disks/)。

## <a name="restrictions"></a>限制

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>下一步

若要瞭解如何變更您的效能層級，請參閱 [入口網站](disks-performance-tiers-portal.md) 或 [PowerShell/CLI](disks-performance-tiers.md) 文章。

