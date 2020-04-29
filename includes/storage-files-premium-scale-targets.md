---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "71180037"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他 premium 檔案共用層級限制

|區域  |目標  |
|---------|---------|
|最小大小增加/減少    |1 GiB      |
|基準 IOPS    |每個 GiB 1 個 IOPS，最多100000|
|IOPS 高載    |每個 GiB 3 倍的 IOPS，最多100000|
|輸出速率         |60 MiB/s + 0.06 * 已布建 GiB        |
|輸入速率| 40 MiB/s + 0.04 * 已布建 GiB |

#### <a name="file-level-limits"></a>檔案層級限制

|區域  |Premium 檔案  |標準檔案 |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|每個檔案的最大 IOPS     |5,000         |1,000|
|並行控制碼    |2,000         |2,000|
|輸出  |300 MiB/秒|      請參閱標準檔案輸送量值|
|輸入  |200 MiB/秒| 請參閱標準檔案輸送量值|
|Throughput| 請參閱 premium 檔案輸入/輸出值| 最高 60 MiB/秒|
