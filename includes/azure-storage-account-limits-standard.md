---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334950"
---
下表描述了 Azure 通用 v1、v2、Blob 存儲、塊 Blob 存儲和啟用資料存儲庫的存儲帳戶的預設限制。 *入口*限制是指發送到存儲帳戶的所有資料。 *出口*限制是指從存儲帳戶接收的所有資料。

| 資源 | 限制 |
| --- | --- |
| 每個訂閱每個區域的存儲帳戶數，包括啟用標準存儲 Gen2 的標準存儲帳戶和資料存儲庫帳戶。<sup>3</sup> | 250 |
| 最大存儲帳戶容量 | 5 PiB <sup>1</sup>|
| 每個存儲帳戶的最大 blob 容器、blob、檔共用、表、佇列、實體或消息數 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 每個存儲帳戶的最大入口<sup>1（</sup>美國、歐洲區域） | 25 Gbps |
| 每個存儲帳戶的最大入口<sup>1（</sup>美國和歐洲以外的區域） | 啟用 RA-GRS/GRS 時為 5 Gbps，LRS/ZRS<sup>2</sup>為 10 Gbps |
| 通用 v2 和 Blob 存儲帳戶的最大出口（所有區域） | 50 Gbps |
| 通用 v1 存儲帳戶的最大出口（美國區域） | 如果啟用 RA-GRS/GRS，則為 20 Gbps，LRS/ZRS<sup>2</sup>的 30 Gbps |
| 通用 v1 存儲帳戶（非美國區域）的最大出口 | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS<sup>2</sup>的 15 Gbps |
| 每個存儲帳戶的最大虛擬網路規則數 | 200 |
| 每個存儲帳戶的最大 IP 位址規則數 | 200 |

<sup>1</sup> Azure 存儲標準帳戶支援更高的容量限制和更高的請求進入限制。 若要要求提高帳戶限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

<sup>2</sup>如果您的存儲帳戶啟用了具有異地冗余存儲 （RA-GRS） 或地理區域冗余存儲 （RA-GZRS） 的讀取存取，則輔助位置的傳出目標與主位置的傳出目標相同。 [Azure 存儲複製](https://docs.microsoft.com/azure/storage/common/storage-redundancy)選項包括：

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure 資料湖存儲 Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)是一組專用於 Azure Blob 存儲的大型資料分析功能。 Azure 存儲和 Blob 存儲限制適用于資料湖存儲第 2 代。

> [!NOTE]
> Microsoft 建議您在大多數方案中使用通用 v2 存儲帳戶。 您可以輕鬆地將通用 v1 或 Azure Blob 存儲帳戶升級到通用 v2 帳戶，無需停機，無需複製資料。 有關詳細資訊，請參閱[升級到通用 v2 存儲帳戶](../articles/storage/common/storage-account-upgrade.md)。

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 有關卷定價的資訊，請參閱[Azure 存儲定價](https://azure.microsoft.com/pricing/details/storage/)。

所有存儲帳戶都運行在平面網路拓撲上，無論創建時間如何。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)。 
