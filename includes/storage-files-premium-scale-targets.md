---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: d704c6026e9d007a7365a3b72649ca509585da4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88057635"
---
#### <a name="additional-premium-file-share-level-limits"></a>其他 premium 檔案共用層級限制

|區域  |目標  |
|---------|---------|
|最小大小增加/減少    |1 GiB      |
|基準 IOPS    |每個 GiB 1 個 IOPS，最多100000個|
|IOPS 高載    |每個 GiB 3 倍的 IOPS，最高100000|
|輸出速率         |60 MiB/s + 0.06 * 布建的 GiB        |
|輸入速率| 40 MiB/s + 0.04 * 布建的 GiB |

#### <a name="file-level-limits"></a>檔案層級限制

|區域  |標準檔案  |Premium 檔案  |
|---------|---------|---------|
|大小     |1 TiB         |4 TiB         |
|每個檔案的最大 IOPS      |1,000         |5,000         |
|並行控制碼     |2,000         |2,000         |
|輸出     |請參閱標準檔案輸送量值         |300 MiB/秒         |
|輸入     |請參閱標準檔案輸送量值         |200 MiB/秒         |
|Throughput     |最高 60 MiB/秒         |查看高階檔案輸入/輸出值         |