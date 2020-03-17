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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371542"
---
Azure 儲存體提供數種儲存體帳戶。 每個類型都支援不同的功能，而且都有自己的計價模式。 您在建立儲存體帳戶之前請先考量這些差異，以判斷您的應用程式最適用的帳戶類型。 儲存體帳戶的類型如下：

- **一般用途 v2 帳戶**：適用於 Blob、文件，佇列和資料表的基本儲存體帳戶類型。 在大部分情況下，建議您使用 Azure 儲存體。
- **一般用途 v1 帳戶**：適用於 Blob、文件，佇列和資料表的傳統帳戶類型。 如果可能的話，請改為使用一般用途 v2 帳戶。
- **BlockBlobStorage 帳戶**：在區塊 Blob 和附加 Blob 上具有進階效能特性的儲存體帳戶。 建議用於具有高交易率的案例，或使用較小物件或需要儲存延遲保持一樣低的案例。
- **FileStorage 帳戶**：具有進階效能特性的僅限檔案儲存體帳戶。 建議用於企業或高效能規模的應用程式。
- **BlobStorage 帳戶**：舊版僅限 Blob 的儲存體帳戶。 如果可能的話，請改為使用一般用途 v2 帳戶。

下表描述儲存體帳戶類型及其功能：

| 儲存體帳戶類型 | 支援的服務                       | 支援的效能層級      | 支援的存取層         | 複寫選項               | 部署模型<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | 加密<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| 一般用途 V2   | Blob、檔案、佇列、資料表、磁碟和 Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | 經常性存取層、非經常性存取層、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS、ZRS、GZRS (預覽)、RA-GZRS (預覽)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | 已加密              |
| 一般用途 V1   | Blob、檔案、佇列、資料表及磁碟       | 標準、進階<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | N/A                            | LRS、GRS、RA-GRS                  | Resource Manager、傳統    | 已加密              |
| BlockBlobStorage   | Blob (僅限區塊 Blob 和附加 Blob) | Premium                       | N/A                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| FileStorage   | 僅限檔案 | Premium                       | N/A                            | LRS、ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | 已加密              |
| BlobStorage         | Blob (僅限區塊 Blob 和附加 Blob) | 標準                      | 經常性存取層、非經常性存取層、封存<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS、GRS、RA-GRS                  | Resource Manager             | 已加密              |

<div id="deployment-model"><sup>1</sup>建議使用 Azure Resource Manager 部署模型。 使用傳統部署模型的儲存體帳戶仍可建立於某些位置，而且會繼續支援現有的傳統帳戶。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager 與傳統部署：了解資源的部署模型和狀態</a>。</div><br/>

<div id="encryption"><sup>2</sup>所有儲存體帳戶會使用待用資料的 Azure 儲存體服務加密 (SSE) 進行加密。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">待用資料的 Azure 儲存體服務加密</a>。</div><br/>

<div id="archive"><sup>3</sup> 封存儲存體與 Blob 等級的階層處理只支援區塊 Blob。 封存儲存層適用於於個別 Blob 的等級，不適用於儲存體帳戶層級。 如需詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob 儲存體：經常性存取、非經常性存取和封存儲存層</a>。</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>區域備援儲存體 (ZRS) 和異地區域備援儲存體 (GZRS/RA-GZRS) (預覽) 僅適用於特定區域中的標準一般用途 V2、BlockBlobStorage 和 FileStorage 帳戶。 如需 Azure 儲存體備援選項的詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure 儲存體備援</a>。</div><br/>

<div id="premium-performance"><sup>5</sup>一般用途 v2 和一般用途 v1 帳戶的進階效能僅適用於磁碟和分頁 Blob。 區塊或附加 Blob 的進階效能僅適用於 BlockBlobStorage 帳戶。 檔案進階效能僅適用於 FileStorage 帳戶。</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 是一組巨量資料分析的專屬功能，內建於 Azure Blob 儲存體。 只有已啟用階層命名空間的一般用途 V2 儲存體帳戶可支援 Data Lake Storage Gen2。 如需 Data Lake Storage Gen2 的詳細資訊，請參閱 <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2 簡介</a>。</div>
