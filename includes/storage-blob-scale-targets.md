---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/08/2019
ms.author: tamram
ms.openlocfilehash: 2ed88d8abb7cbe96093b68d89030e6e464a35541
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392305"
---
| 資源 | 目標        |
|----------|---------------|
| 單個 Blob 容器的最大大小 | 與最大存儲帳戶容量相同 |
| 塊 blob 或追加 Blob 中的最大塊數 | 50,000 個區塊 |
| 塊 Blob 中塊的最大大小 | 100 MiB |
| 塊 Blob 的最大大小 | 50，000 X 100 MiB （約 4.75 TiB） |
| 追加 Blob 中塊的最大大小 | 4 MiB |
| 追加 Blob 的最大大小 | 50，000 x 4 米貝 （約 195 GiB） |
| 頁面 Blob 的最大大小 | 8 TiB |
| 每個 Blob 容器存儲訪問策略的最大數量 | 5 |
|單個 Blob 的目標請求速率 | 每秒最多 500 個請求 |
|單個頁面 Blob 的目標輸送量 | 每秒最多 60 MiB |
|單個塊 Blob 的目標輸送量 |高達存儲帳戶入口/出口限制<sup>1</sup> |

<sup>1</sup>單個 Blob 的輸送量取決於幾個因素，包括但不限於：併發性、請求大小、性能層、上載源速度和下載目標。 要利用[高通量塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)的性能增強，請上載較大的 blob 或塊。 具體來說，對於標準存儲帳戶，使用大於 4 MiB 的 Blob 或塊大小調用["放置 Blob"](/rest/api/storageservices/put-blob)或["放置塊"](/rest/api/storageservices/put-block)操作。 對於高級塊 Blob 或 Data Lake 存儲 Gen2 存儲帳戶，請使用大於 256 KiB 的塊或 blob 大小。
