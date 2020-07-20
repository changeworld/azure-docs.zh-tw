---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/08/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 509568b143c9fbbf236139ca83cb55b0ef39beb0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145949"
---
下表說明 Azure 一般用途 v1、v2、Blob 儲存體及區塊 Blob 儲存體帳戶的預設限制。 「輸入」限制是指傳送至某個儲存體帳戶的所有資料。 「輸出」限制是指從儲存體帳戶收到的所有資料。

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

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 如需批量價格的詳細資訊，請參閱 [Azure 儲存體價格](https://azure.microsoft.com/pricing/details/storage/)。

所有儲存體帳戶都會在平面網路拓撲上執行，不論其建立時間為何。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets) \(英文\)。 
