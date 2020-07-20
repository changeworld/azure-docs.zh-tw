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
ms.openlocfilehash: 83c391c0d92f8d4a0ed4b44bc3a90273db51b412
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81539611"
---
增量快照集是受控磁片的時間點備份，在這種情況下，只會包含自上一個快照集之後的所有變更。 當您嘗試下載或使用增量快照集時，會使用完整的 VHD。 受控磁片快照集的這項新功能可能會讓它們更符合成本效益，因為除非您選擇，否則您不需要將整個磁片與每個個別的快照集一起儲存。 就像一般快照集一樣，您可以使用增量快照集來建立完整的受控磁片，或建立一般的快照集。

增量快照集和一般快照集之間有一些差異。 增量快照集會一律使用標準 Hdd 存放裝置，而不考慮磁片的儲存體類型，而一般快照集可以使用 premium Ssd。 如果您在進階儲存體上使用一般快照集來相應增加 VM 部署，建議您在[共用映射資源庫](../articles/virtual-machines/linux/shared-image-galleries.md)中的標準儲存體上使用自訂映射。 它可協助您以較低的成本達成更大規模的規模。 此外，增量快照集可能會透過[區域冗余儲存體](../articles/storage/common/storage-redundancy-zrs.md)（ZRS）提供更好的可靠性。 如果選取的區域中有可用的 ZRS，則增量快照集會自動使用 ZRS。 如果 ZRS 無法在區域中使用，則快照集會預設為[本機多餘的儲存體](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以覆寫此行為，並手動選取一個，但我們不建議這麼做。

增量快照集也提供差異功能，僅適用于受控磁片。 它們可讓您將相同受控磁片的兩個增量快照集之間的變更，減少到區塊層級。 您可以使用這項功能來減少跨區域複製快照集時的資料使用量。  例如，您可以下載第一個增量快照集作為另一個區域中的基底 blob。 針對後續的增量快照集，您可以只將上一個快照集之後的變更複製到基底 blob。 複製變更之後，您可以在基底 blob 上拍攝快照，以代表另一個區域中磁片的時間點備份。 您可以從基底 blob，或從另一個區域中基底 blob 的快照集復原磁碟。

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="描述跨區域複製增量快照集的圖表。快照集會進行各種 API 呼叫，直到最後形成每個快照集的分頁 blob 為止。":::

您可以藉由檢查 [Azure 使用量報表](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)來查看已使用的快照大小。 例如，如果快照集的已使用資料大小為 10 GiB，則**每日**使用量報表會顯示已使用數量為 10 GiB/(31 天) = 0.3226。
