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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79371542"
---
Azure 儲存體提供數種類型的儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的類型如下：

- **一般用途 v2 帳戶**： blob、檔案、佇列和資料表的基本儲存體帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
- **一般用途 v1 帳戶**： blob、檔案、佇列和資料表的舊版帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
- **BlockBlobStorage 帳戶**：儲存體帳戶，具有適用于區塊 blob 和附加 blob 的 premium 效能特性。 建議適用于具有高交易率的案例，或使用較小物件或需要持續低儲存延遲的案例。
- **FileStorage 帳戶**：僅限檔案的儲存體帳戶，具有 premium 效能特性。 建議用於企業或高效能規模的應用程式。
- **BlobStorage 帳戶**：舊版僅限 Blob 的儲存體帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。

下表描述儲存體帳戶類型及其功能：

| 儲存體帳戶類型 | 支援的服務                       | 支援的效能層級      | 支援的存取層         | 複寫選項               | 部署模型<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 加密<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 一般用途 V2   | Blob、檔案、佇列、資料表、磁片和 Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 經常性存取層、非經常性存取層、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、切換（預覽）、RA-切換（預覽）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | 已加密              |
| 一般用途 V1   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS、GRS、RA-GRS                  | Resource Manager、傳統    | 已加密              |
| BlockBlobStorage   | Blob (僅限區塊 Blob 和附加 Blob) | Premium                       | N/A                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| FileStorage   | 僅檔案 | Premium                       | N/A                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| BlobStorage         | Blob (僅限區塊 Blob 和附加 Blob) | 標準                      | 經常性存取層、非經常性存取層、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | Resource Manager             | 已加密              |

<div id="deployment-model"><sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. 傳統部署：了解資源的部署模型和狀態</a>。</div><br/>

<div id="encryption"><sup>2</sup>所有儲存體帳戶會使用待用資料的 Azure 儲存體服務加密 (SSE) 進行加密。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">待用資料的 Azure 儲存體服務加密</a>。</div><br/>

<div id="archive"><sup>3</sup>封存儲存體和 blob 層級分層僅支援區塊 blob。 封存層僅適用于個別 blob 的層級，而不是在儲存體帳戶層級。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob 儲存體︰經常性存取、非經常性存取和封存儲存層</a>。</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>區域冗余儲存體（ZRS）和地理區域冗余儲存體（切換/RA-切換）（預覽）僅適用于特定區域中的標準一般用途 V2、BlockBlobStorage 和 FileStorage 帳戶。 如需 Azure 儲存體冗余選項的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure 儲存體冗余</a>。</div><br/>

<div id="premium-performance"><sup>5</sup>一般用途 v2 和一般用途 v1 帳戶的 Premium 效能僅適用于磁片和分頁 blob。 「封鎖」或「附加」 blob 的 Premium 效能僅適用于 BlockBlobStorage 帳戶。 檔案的 Premium 效能僅適用于 FileStorage 帳戶。</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 是一組專供大型資料分析使用的功能，建置於 Azure Blob 儲存體上。 只有在啟用階層命名空間的一般用途 V2 儲存體帳戶上，才支援 Data Lake Storage Gen2。 如需 Data Lake Storage Gen2 的詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 簡介</a>。</div>
