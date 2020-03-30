---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180037"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他高級檔共用級別限制

|區域  |目標  |
|---------|---------|
|最小尺寸增加/減少    |1 吉布      |
|基準 IOPS    |每個 GiB 1 IOPS，最多 100，000 個|
|IOPS 爆裂    |每個 GiB 3x IOPS，最多 100，000 個|
|出入口率         |60 MiB/s = 0.06 = 預配 GiB        |
|入口率| 40 MiB/s = 0.04 = 預配 GiB |

#### <a name="file-level-limits"></a>檔級別限制

|區域  |高級檔  |標準檔 |
|---------|---------|---------|
|大小                  |1 TiB         |1 TiB|
|每個檔的最大 IOPS     |5,000         |1,000|
|併發控制碼    |2,000         |2,000|
|輸出  |300 米B/秒|      請參閱標準檔輸送量值|
|輸入  |200 MiB/秒| 請參閱標準檔輸送量值|
|Throughput| 查看高級檔入口/出口值| 最高 60 MiB/秒|
