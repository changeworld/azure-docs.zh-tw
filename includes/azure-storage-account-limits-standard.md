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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334950"
---
下表說明 Azure 一般用途 v1、v2、Blob 儲存體、區塊 Blob 儲存體，以及 Data Lake Storage Gen2 啟用的儲存體帳戶的預設限制。 輸入*限制是*指傳送至儲存體帳戶的所有資料。 輸出*限制是*指從儲存體帳戶接收的所有資料。

| 資源 | 限制 |
| --- | --- |
| 每個訂用帳戶每個區域的儲存體帳戶數目，包括 standard、premium 和 Data Lake Storage Gen2 啟用的儲存體帳戶。<sup>3</sup> | 250 |
| 儲存體帳戶容量上限 | 5 PiB <sup>1</sup>|
| 每個儲存體帳戶的 blob 容器、blob、檔案共用、資料表、佇列、實體或訊息數目上限 | 沒有限制 |
| 每一儲存體帳戶的要求率上限<sup>1</sup> | 每秒 20,000 個要求 |
| 每一儲存體帳戶的輸入上限<sup>1</sup> （美國、歐洲地區） | 25 Gbps |
| 每一儲存體帳戶的輸入上限<sup>1</sup> （美國和歐洲以外的地區） | 如果啟用了 RA-GRS/GRS，則為 5 gbps，LRS/ZRS 為 10 Gbps<sup>2</sup> |
| 一般用途 v2 和 Blob 儲存體帳戶的輸出上限（所有區域） | 50 Gbps |
| 一般用途 v1 儲存體帳戶的輸出上限（美國地區） | 如果啟用了 RA-GRS/GRS，則為 20 gbps，LRS/ZRS 為 30 Gbps<sup>2</sup> |
| 一般用途 v1 儲存體帳戶的輸出上限（非美國地區） | 如果啟用了 RA-GRS/GRS，則為 10 gbps，LRS/ZRS 為 15 Gbps<sup>2</sup> |
| 每個儲存體帳戶的虛擬網路規則數目上限 | 200 |
| 每個儲存體帳戶的 IP 位址規則數目上限 | 200 |

<sup>1</sup> Azure 儲存體標準帳戶支援更高的容量限制，以及依要求輸入的更高限制。 若要要求提高帳戶限制，請連絡 [Azure 支援](https://azure.microsoft.com/support/faq/)。

<sup>2</sup>如果您的儲存體帳戶已啟用異地多餘儲存體（RA-GRS）或異地區域冗余儲存體（RA-切換）的讀取權限，則次要位置的輸出目標會與主要位置相同。 [Azure 儲存體](https://docs.microsoft.com/azure/storage/common/storage-redundancy)複寫選項包括：

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md)是一組專供大型資料分析使用的功能，建置於 Azure Blob 儲存體上。 Azure 儲存體和 blob 儲存體限制適用于 Data Lake Storage Gen2。

> [!NOTE]
> Microsoft 建議您在大部分情況下使用一般用途 v2 儲存體帳戶。 您可以輕鬆地將一般用途 v1 或 Azure Blob 儲存體帳戶升級至一般用途 v2 帳戶，而不需停機，也不需要複製資料。 如需詳細資訊，請參閱[升級至一般用途 v2 儲存體帳戶](../articles/storage/common/storage-account-upgrade.md)。

如果您應用程式的需求超過單一儲存體帳戶的延展性目標，您可以將應用程式建置為使用多個儲存體帳戶。 接著您可以在這些儲存體帳戶之間分割資料物件。 如需有關大量價格的資訊，請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/)。

所有儲存體帳戶都會在一般網路拓撲上執行，不論其建立時間為何。 如需 Azure 儲存體平面網路架構及延展性的詳細資訊，請參閱 [Microsoft Azure 儲存體：具有高度一致性的高可用性雲端儲存體服務](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets)。 
