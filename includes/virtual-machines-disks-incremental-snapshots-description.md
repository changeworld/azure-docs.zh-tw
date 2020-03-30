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
ms.openlocfilehash: 4d2c0a02a48c0e04b501f136f66c28b3f532e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486123"
---
增量快照是託管磁片的時間點備份，在拍攝時，這些磁片僅包含自上次快照以來的所有更改。 當您嘗試下載或以其他方式使用增量快照時，將使用完整的 VHD。 託管磁片快照的這種新功能可能使它們更具成本效益，因為除非您選擇這樣做，否則不必將整個磁片與每個單獨的快照一起存儲。 與常規快照一樣，增量快照可用於創建完整的託管磁片或製作常規快照。

增量快照和常規快照之間存在一些差異。 增量快照將始終使用標準 HDD 存儲，而不管磁片的存儲類型如何，而常規快照可以使用高級 SSD。 如果您在高級存儲上使用常規快照來擴展 VM 部署，我們建議您在[共用映射庫中](../articles/virtual-machines/linux/shared-image-galleries.md)的標準存儲上使用自訂映射。 它將説明您以更低的成本實現更大規模的規模。 此外，增量快照可能通過[區域冗余存儲](../articles/storage/common/storage-redundancy-zrs.md)（ZRS） 提供更好的可靠性。 如果 ZRS 在選定區域中可用，增量快照將自動使用 ZRS。 如果 ZRS 在該區域中不可用，則快照將預設為[本地冗余存儲](../articles/storage/common/storage-redundancy-lrs.md)（LRS）。 您可以重寫此行為並手動選擇一個，但我們不建議這樣做。

增量快照還提供差分功能，僅適用于託管磁片。 它們使您能夠獲取同一託管磁片的兩個增量快照之間的更改，下到塊級別。 在跨區域複製快照時，可以使用此功能來減少資料佔用空間。  例如，您可以將第一個增量快照下載為另一個區域中的基本 blob。 對於後續增量快照，只能將自上次快照以來的更改複製到基本 Blob。 複製更改後，可以在基本 Blob 上拍攝表示您在另一個區域中磁片的時間點備份的快照。 可以從基本 Blob 或從其他區域中基本 Blob 上的快照復原磁碟。

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="描述跨區域複製的增量快照的圖表。快照進行各種 API 呼叫，直到最終形成每個快照的頁面 blob。":::