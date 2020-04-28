---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "75392305"
---
| 資源 | 目標        |
|----------|---------------|
| 單一 blob 容器的大小上限 | 與儲存體帳戶容量上限相同 |
| 區塊 blob 或附加 blob 中的區塊數目上限 | 50,000 個區塊 |
| 區塊 blob 中的區塊大小上限 | 100 MiB |
| 區塊 blob 的大小上限 | 50000 X 100 MiB （約 4.75 TiB） |
| 附加 blob 中的區塊大小上限 | 4 MiB |
| 附加 blob 的大小上限 | 50000 x 4 MiB （約 195 GiB） |
| 分頁 blob 的大小上限 | 8 TiB |
| 每個 blob 容器的預存存取原則數目上限 | 5 |
|單一 blob 的目標要求率 | 每秒最多500個要求 |
|單一分頁 blob 的目標輸送量 | 每秒最多 60 MiB |
|單一區塊 blob 的目標輸送量 |最高可達儲存體帳戶的輸入/輸出限制<sup>1</sup> |

<sup>1</sup>單一 blob 的輸送量取決於數個因素，包括但不限於平行存取、要求大小、效能層級、上傳來源的速度，以及下載的目的地。 若要利用[高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)的效能增強功能，請上傳較大的 blob 或區塊。 具體而言，請針對標準儲存體帳戶，使用大於 4 MiB 的 blob 或區塊大小來呼叫[Put blob](/rest/api/storageservices/put-blob)或[put block](/rest/api/storageservices/put-block)作業。 針對 premium 區塊 blob 或 Data Lake Storage Gen2 儲存體帳戶，請使用大於 256 KiB 的區塊或 blob 大小。
