---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: tamram
ms.openlocfilehash: fe493ca4a2edf16bfda405e8b2c5581c0f745616
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665584"
---
| 資源 | 目標 | 目標 (預覽) |
|-|-|-|
| 單一 Blob 容器的大小上限 | 與儲存體帳戶容量上限相同 |  |
| 區塊 Blob 或附加 Blob 中的區塊數目上限 | 50,000 個區塊 |  |
| 區塊 Blob 中的區塊大小上限 | 100 MiB | 4000 MiB (預覽) |
| 區塊 Blob 的大小上限 | 50,000 X 100 MiB (大約 4.75 TiB) | 50,000 X 4000 MiB (大約 190.7 TiB) (預覽) |
| 附加 Blob 中的區塊大小上限 | 4 MiB |  |
| 附加 Blob 的大小上限 | 50,000 x 4 MiB (大約 195 GiB) |  |
| 分頁 Blob 的大小上限 | 8 TiB<sup>2</sup> |  |
| 每個 Blob 容器的預存存取原則的最大數目 | 5 |  |
| 單一 Blob 的目標要求率 | 每秒最多 500 個要求 |  |
| 單一頁面 Blob 的目標輸送量 | 每秒最多 60 MiB<sup>2</sup> |  |
| 單一區塊 Blob 的目標輸送量 | 最高儲存體帳戶的輸入/輸出限制<sup>1</sup> |  |

<sup>1</sup> 單一 Blob 的輸送量取決於數個因素，包括 (但不限於)：並行、要求大小、效能層、上傳來源速度，以及下載的目的地。 若要利用[高輸送量區塊 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) 的效能增強功能，請上傳較大的 Blob 或區塊。 具體而言，請針對標準儲存體帳戶，呼叫 [Put Blob](/rest/api/storageservices/put-blob) 或 [Put Block](/rest/api/storageservices/put-block) 作業，其 Blob 或區塊大小大於 4 MiB。 針對高階區塊 Blob 或 Data Lake Storage Gen2 儲存體帳戶，使用大於 256 KiB 的區塊或 Blob 大小。

<sup>2</sup> 具有**階層命名空間**設定的帳戶尚未支援分頁 Blob。

下表描述服務版本所允許的最大區塊和 Blob 大小。

| 服務版本 | 區塊大小上限 (透過 Put Block) | Blob 大小上限 (透過 Put Block List) | 透過單一寫入作業的 Blob 大小上限 (透過 Put Blob) |
|-|-|-|-|
| 2019-12-12 版和更新版本 | 4000 MiB (預覽) | 大約 190.7 TiB (4000 MiB X 50,000 個區塊) (預覽) | 5000 MiB (預覽) |
| 2016-05-31 版至 2019-07-07 版 | 100 MiB | 大約 4.75 TiB (100 MiB X 50,000 個區塊) | 256 MiB |
| 2016-05-31 之前的版本 | 4 MiB | 大約 195 GiB (4 MiB X 50,000 個區塊) | 64 MiB |
