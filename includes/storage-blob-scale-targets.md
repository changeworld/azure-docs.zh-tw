---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/23/2020
ms.author: tamram
ms.openlocfilehash: 43a72d915fa5a00c54bef7a06fe3815a7d63f2fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85805705"
---
| 資源 | 目標 | 目標（預覽） |
|-|-|-|
| 單一 blob 容器的大小上限 | 與儲存體帳戶容量上限相同 |  |
| 區塊 blob 或附加 blob 中的區塊數目上限 | 50,000 個區塊 |  |
| 區塊 blob 中的區塊大小上限 | 100 MiB | 4000 MiB （預覽） |
| 區塊 blob 的大小上限 | 50000 X 100 MiB （約 4.75 TiB） | 50000 X 4000 MiB （約 190.7 TiB）（預覽） |
| 附加 blob 中的區塊大小上限 | 4 MiB |  |
| 附加 blob 的大小上限 | 50000 x 4 MiB （約 195 GiB） |  |
| 分頁 blob 的大小上限 | 8 TiB |  |
| 每個 blob 容器的預存存取原則數目上限 | 5 |  |
| 單一 blob 的目標要求率 | 每秒最多500個要求 |  |
| 單一分頁 blob 的目標輸送量 | 每秒最多 60 MiB |  |
| 單一區塊 blob 的目標輸送量 | 最高可達儲存體帳戶的輸入/輸出限制<sup>1</sup> |  |

<sup>1</sup>單一 blob 的輸送量取決於數個因素，包括但不限於平行存取、要求大小、效能層級、上傳來源的速度，以及下載的目的地。 若要利用[高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)的效能增強功能，請上傳較大的 blob 或區塊。 具體而言，請針對標準儲存體帳戶，使用大於 4 MiB 的 blob 或區塊大小來呼叫[Put blob](/rest/api/storageservices/put-blob)或[put block](/rest/api/storageservices/put-block)作業。 針對 premium 區塊 blob 或 Data Lake Storage Gen2 儲存體帳戶，請使用大於 256 KiB 的區塊或 blob 大小。

下表描述服務版本所允許的最大區塊和 blob 大小。

| 服務版本 | 區塊大小上限（透過 Put 區塊） | Blob 大小上限（透過 Put 封鎖清單） | 透過單一寫入作業的最大 blob 大小（經由 Put Blob） |
|-|-|-|-|
| 版本2019-12-12 和更新版本 | 4000 MiB （預覽） | 大約 190.7 TiB （4000 MiB X 50000 區塊）（預覽） | 5000 MiB （預覽） |
| 版本2016-05-31 至2019-07-07 版 | 100 MiB | 大約 4.75 TiB （100 MiB X 50000 區塊） | 256 MiB |
| 2016-05-31 之前的版本 | 4 MiB | 大約 195 GiB （4 MiB X 50000 區塊） | 64 MiB |
