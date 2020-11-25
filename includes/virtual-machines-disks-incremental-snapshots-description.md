---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: edec192009b9cc9b71114287e705c161183273dc
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95992857"
---
增量快照集是受控磁片的時間點備份，在進行時，只會包含自上一個快照集以來的變更。 當您從增量快照集復原磁碟時，系統會重建完整磁片，代表取得增量快照集時磁片的時間點備份。 受控磁片快照集的這項新功能可能會讓它們更符合成本效益，因為除非您選擇，否則您不需要在每個個別快照集儲存整個磁片。 如同完整的快照集，增量快照集可以用來建立完整的受控磁片或完整的快照集。

增量快照集與完整快照之間有一些差異。 增量快照集一律會使用標準 Hdd 儲存體（不論磁片的儲存類型），而完整快照集可以使用 premium Ssd。 如果您在進階儲存體上使用完整快照集來擴大 VM 部署，建議您在 [共用映射庫](../articles/virtual-machines/linux/shared-image-galleries.md)中的標準儲存體上使用自訂映射。 它將協助您以較低的成本達成更大規模的規模。 此外，增量快照集可能會提供更佳的可靠性，以及 [區域冗余儲存體](../articles/storage/common/storage-redundancy.md) (ZRS) 。 如果選取的區域中有可用的 ZRS，增量快照將會自動使用 ZRS。 如果區域中無法使用 ZRS，則快照集將預設為 [本機的儲存體](../articles/storage/common/storage-redundancy.md) (LRS) 。 您可以覆寫此行為，並手動選取其中一個，但不建議這麼做。

增量快照集也提供差異功能，僅適用于受控磁片。 它們可讓您將相同受控磁片的兩個增量快照之間的變更，減少到區塊層級。 您可以使用這項功能，在跨區域複製快照集時減少資料使用量。  例如，您可以將第一個增量快照集下載為另一個區域中的基底 blob。 針對後續的累加快照集，您只能將自上一個快照集以來的變更複製到基底 blob。 複製變更之後，您可以在基底 blob 上拍攝快照集，以代表另一個區域中磁片的時間點備份。 您可以從基底 blob，或從另一個區域中基底 blob 的快照集還原您的磁片。

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="描述跨區域複製增量快照集的圖表。快照集會進行各種 API 呼叫，直到最後針對每個快照集形成分頁 blob 為止。":::

增量快照集只會以使用的大小計費。 您可以查看 [Azure 使用量報表](../articles/cost-management-billing/understand/review-individual-bill.md)，以找出已使用的快照大小。 例如，如果快照集的已使用資料大小為 10 GiB，則 **每日** 使用量報表會顯示已使用數量為 10 GiB/(31 天) = 0.3226。