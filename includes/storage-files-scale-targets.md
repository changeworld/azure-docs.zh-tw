---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 8a8619da831dfa5b240bd93d3a046c49cc30affa
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901432"
---
| 資源 | 標準檔案共用 | Premium 檔案共用 |
|----------|---------------|------------------------------------------|
| 檔案共用大小上限 | 沒有最小值;隨用隨付 | 100 GiB;預 |
| 檔案共用的大小上限 | 100 TiB *、5 TiB | 100 TiB |
| 檔案共用中檔案的大小上限 | 1 TiB | 1 TiB |
| 檔案共用中的檔案數目上限 | 無限制 | 無限制 |
| 每個共用的最大 IOPS | 10000 IOPS *，1000 IOPS | 100,000 IOPS |
| 每個檔案共用的儲存存取原則數目上限 | 5 | 5 |
| 單一檔案共用的目標輸送量 | 最高 300 MiB/秒 *，最高 60 MiB/秒，  | 請參閱 premium 檔案共用輸入和輸出值|
| 單一檔案共用的輸出上限 | 請參閱標準檔案共用目標輸送量 | 最高 6204 MiB/秒 |
| 單一檔案共用的輸入上限 | 請參閱標準檔案共用目標輸送量 | 最高 4136 MiB/秒 |
| 每個檔案的開啟控制代碼數目上限 | 2,000 個開啟控制代碼 | 2,000 個開啟控制代碼 |
| 共用快照集的數目上限 | 200 個共用快照集 | 200 個共用快照集 |
| 物件 (目錄和檔案) 名稱長度上限 | 2048個字元 | 2048個字元 |
| 最大路徑名稱元件 (在路徑 \A\B\C\D 中，每個字母都是元件) | 255 個字元 | 255 個字元 |

在大部分區域中都有提供 \*，請參閱[區域可用性](../articles/storage/files/storage-files-planning.md#regional-availability)以取得可用區域的詳細資料。
