---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 6cf9be653da2dd587b93724b6e319dc4d20686c2
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536420"
---
| 資源 | 標準檔案共用 | 進階檔案分享 |
|----------|---------------|------------------------------------------|
| 檔案共用大小上限 | 無最低值;付你走 | 100 GiB;供應 |
| 檔案共用的最大大小 | 100 TiB*, 5 TiB | 100 TiB |
| 檔案分享中檔案的最大大小 | 1 TiB | 1 TiB |
| 檔案分享中檔案的最大數量 | 沒有限制 | 沒有限制 |
| 每股最大 IOPS | 10,000 IOPS*,1,000 IOPS | 100,000 IOPS |
| 每個檔案分享的最大儲存存取策略數 | 5 | 5 |
| 單一檔案共享的目標 | 高達 300 公尺/秒*,高達 60 MiB/秒 ,  | 檢視進階檔案分享入口和出口值|
| 單個檔案共用的最大出口 | 請參考標準檔案共享目標輸送量 | 高達 6,204 公尺/斯 |
| 單一檔案分享的最大區 | 請參考標準檔案共享目標輸送量 | 高達 4,136 公尺/斯 |
| 每個檔案的開啟控制代碼數目上限 | 2,000 個開啟控制代碼 | 2,000 個開啟控制代碼 |
| 共用快照集的數目上限 | 200 個共用快照集 | 200 個共用快照集 |
| 物件 (目錄和檔案) 名稱長度上限 | 2,048 個字元 | 2,048 個字元 |
| 最大路徑名稱元件 (在路徑 \A\B\C\D 中，每個字母都是元件) | 255 個字元 | 255 個字元 |

\*預設標準檔案共用為 5 TiB,請參閱[啟用和創建大型檔案共用](../articles/storage/files/storage-files-how-to-create-large-file-share.md),瞭解如何將標準檔案共用放大至 100 TiB 的詳細資訊。
