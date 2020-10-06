---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8c908b7f72e5581122427a7d711ae9cf61d2d90f
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665583"
---
下表說明 Azure 一般用途 v1、v2、Blob 儲存體及區塊 Blob 儲存體帳戶的預設限制。 「輸入」限制是指傳送至某個儲存體帳戶的所有資料。 「輸出」限制是指從儲存體帳戶收到的所有資料。

> [!NOTE]
> 您可以要求更高的容量和輸入限制。 若要要求提高，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

| 資源 | 限制 |
| --- | --- |
| 每個區域中每個訂用帳戶的儲存體帳戶數目，包括標準和進階儲存體帳戶。| 250 |
| 儲存體帳戶容量上限 | 5 PiB <sup>1</sup>|
| 每一儲存體帳戶的 Blob 容器、Blob、檔案共用、資料表、佇列、實體或訊息的數目上限 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 每一儲存體帳戶的輸入上限 <sup>1</sup> (美國、歐洲區域) | 10 Gbps |
| 每一儲存體帳戶的輸入上限<sup>1</sup> (美國和歐洲以外的區域) | 如果啟用 RA-GRS/GRS，則為 5 Gbps，LRS/ZRS 為 10 Gbps<sup>2</sup> |
| 一般用途 v2 和 Blob 儲存體帳戶的輸出上限 (所有區域) | 50 Gbps |
| 一般用途 v1 儲存體帳戶的輸出上限 (美國區域) | 如果啟用 RA-GRS/GRS，則為 20 Gbps，LRS/ZRS 為 30 Gbps<sup>2</sup> |
| 一般用途 v1 儲存體帳戶的輸出上限 (非美國區域) | 如果啟用 RA-GRS/GRS，則為 10 Gbps，LRS/ZRS 為 15 Gbps<sup>2</sup> |
| 每一儲存體帳戶的虛擬網路規則數目上限 | 200 |
| 每一儲存體帳戶的 IP 位址規則數目上限 | 200 |

<sup>1</sup> Azure 儲存體標準帳戶會依照要求支援較高的容量限制和較高的輸入限制。 若要要求提高帳戶限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

<sup>2</sup> 如果您的儲存體帳戶已搭配異地備援儲存體 (RA-GRS) 或異地區域備援儲存體 (RA-GZRS) 啟用讀取權限，則次要位置的輸出目標會與主要位置的輸出目標相同。 如需詳細資訊，請參閱 [Azure 儲存體複寫](../articles/storage/common/storage-redundancy.md)。

> [!NOTE]
> Microsoft 建議，您應在大部分情況下使用一般用途 v2 儲存體帳戶。 您不需停機，也不必複製資料，即可輕鬆地將一般用途 v1 或 Azure Blob 儲存體帳戶升級至一般用途 v2 帳戶。 如需詳細資訊，請參閱[升級至一般用途 v2 儲存體帳戶](../articles/storage/common/storage-account-upgrade.md)。

所有儲存體帳戶都會在平面網路拓撲上執行，不論其建立時間為何。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) \(英文\)。 

