---
title: 包含檔案
description: 包含檔案
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371542"
---
Azure 存儲提供多種類型的存儲帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的類型如下：

- **通用 v2 帳戶**：Blob、檔、佇列和表的基本存放裝置帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
- **通用 v1 帳戶**：Blob、檔、佇列和表的舊帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
- **塊 Blob 存儲帳戶**：具有塊 blob 和追加 blob 的高級性能特徵的存儲帳戶。 建議適用于事務速率高的方案，或使用較小物件或需要始終低存儲延遲的方案。
- **檔存儲帳戶**：僅檔存儲帳戶具有高級性能特徵。 推薦用於企業或高性能擴展應用程式。
- **Blob 存儲帳戶**：僅存儲的舊存儲帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。

下表描述儲存體帳戶類型及其功能：

| 儲存體帳戶類型 | 支援的服務                       | 支援的效能層級      | 支援的存取層         | 複寫選項               | 部署模型<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 加密<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 一般用途 V2   | Blob、檔、佇列、表、磁片和資料湖第 2 代<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 熱， 酷， 存檔<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、GZRS（預覽）、RA-GZRS（預覽）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | 已加密              |
| 一般用途 V1   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS、GRS、RA-GRS                  | Resource Manager、傳統    | 已加密              |
| 塊 Blob 存儲   | Blob (僅限區塊 Blob 和附加 Blob) | Premium                       | N/A                            | LRS， ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| 檔存儲   | 僅檔案 | Premium                       | N/A                            | LRS， ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| BlobStorage         | Blob (僅限區塊 Blob 和附加 Blob) | 標準                      | 熱， 酷， 存檔<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | Resource Manager             | 已加密              |

<div id="deployment-model"><sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態</a>。</div><br/>

<div id="encryption"><sup>2</sup>所有儲存體帳戶會使用待用資料的 Azure 儲存體服務加密 (SSE) 進行加密。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">待用資料的 Azure 儲存體服務加密</a>。</div><br/>

<div id="archive"><sup>3</sup>存檔存儲和 blob 級分層僅支援塊 Blob。 存檔層僅在單個 Blob 級別可用，而不是在存儲帳戶級別。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob 儲存體︰經常性存取、非經常性存取和封存儲存層</a>。</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>區域冗余存儲 （ZRS） 和地理位置區域冗余存儲 （GZRS/RA-GZRS） （預覽）僅適用于特定區域的標準通用 V2、BlockBlob 存儲和檔存儲帳戶。 有關 Azure 存儲冗余選項的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure 存儲冗余</a>。</div><br/>

<div id="premium-performance"><sup>5</sup>通用 v2 和通用 v1 帳戶的高級性能僅適用于磁片和頁面 Blob。 塊或追加 blob 的高級性能僅在塊 Blob 存儲帳戶上可用。 檔的高級性能僅在檔存儲帳戶上可用。</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure 資料湖存儲 Gen2 是一組專用於 Azure Blob 存儲的大型資料分析功能。 資料湖存儲 Gen2 僅在啟用階層命名空間的通用 V2 存儲帳戶上受支援。 有關資料存儲第 2 代的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure 資料存儲湖存儲第 2 代簡介</a>。</div>
