---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/10/2020
ms.author: rogarana
ms.openlocfilehash: 8dcb58499113b0b7ae0814419f0a76965a0ed945
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681070"
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
|每個檔案的最大 IOPS      |1,000         |最高 8000 *         |
|並行控制碼     |2,000         |2,000         |
|輸出     |請參閱標準檔案輸送量值         |300 MiB/秒 (多達1個具有 SMB 多重通道預覽的 GiB/秒) * *         |
|輸入     |請參閱標準檔案輸送量值         |200 MiB/秒 (多達1個具有 SMB 多重通道預覽的 GiB/秒) * *        |
|輸送量     |最高 60 MiB/秒         |查看高階檔案輸入/輸出值         |

\*<sup>適用于讀取和寫入 io (通常是較小的 IO 大小 <= 64k) 。除了讀取和寫入以外的中繼資料作業可能較低。</sup>

\*\*<sup>受限於電腦網路限制、可用頻寬、IO 大小、佇列深度和其他因素。如需詳細資訊，請參閱[SMB 多重通道效能](../articles/storage/files/storage-files-smb-multichannel-performance.md)。</sup>
