---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334979"
---
**進階非受控虛擬機器磁碟：每個帳戶的限制**

| 資源 | 限制 |
| --- | --- |
| 每一帳戶的磁碟容量總計 |35 TB |
| 每一帳戶的快照集容量總計 |10 TB |
| 每一帳戶的最大頻寬 (輸入 + 輸出)<sup>1</sup> |<=50 Gbps |

<sup>1</sup>「輸入」是指傳送至儲存體帳戶的要求中的所有資料。 「輸出」是指從儲存體帳戶接收的回應中的所有資料。

**進階非受控虛擬機器磁碟：每一磁碟的限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁碟大小 |128 GiB |512 GiB |1,024 GiB (1 TB) |2,048 GiB (2 TB)|4,095 GiB (4 TB)|
| 每一磁碟的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每一磁碟的輸送量上限 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每個儲存體帳戶的磁碟數目上限 |280 |70 |35 | 17 | 8 |

**進階非受控虛擬機器磁碟：每一 VM 的限制**

| 資源 | 限制 |
| --- | --- |
| 每一 VM 的最大 IOPS |80,000 IOPS (使用 GS5 虛擬機器) |
| 每一 VM 的輸送量上限 |2,000 MB/秒 (使用 GS5 虛擬機器) |

