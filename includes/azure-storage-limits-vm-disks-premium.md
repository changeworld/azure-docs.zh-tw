---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: rogarana
ms.openlocfilehash: ef18feb10dabc6a77e6512c6a32ad44b32c6e832
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334979"
---
**高級非託管虛擬機器磁片：每個帳戶限制**

| 資源 | 限制 |
| --- | --- |
| 每一帳戶的磁碟容量總計 |35 TB |
| 每一帳戶的快照集容量總計 |10 TB |
| 每個帳戶的最大頻寬（入口 + 出口）<sup>1</sup> |<=50 Gbps |

<sup>1</sup>*入口*是指發送到存儲帳戶的請求的所有資料。 *"退出"* 是指從存儲帳戶接收的回應的所有資料。

**高級非託管虛擬機器磁片：每磁片限制**

| 進階儲存體磁碟類型 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- |
| 磁碟大小 |128 GB |512 GB |1，024 千巴 （1 TB） |2，048 千巴 （2 TB）|4，095 千巴 （4 TB）|
| 每個磁片的最大 IOPS |500 |2,300 |5,000 |7,500 |7,500 |
| 每個磁片的最大輸送量 |100 MB/秒 | 150 MB/秒 |200 MB/秒 |250 MB/秒 |250 MB/秒 |
| 每個存儲帳戶的最大磁片數 |280 |70 |35 | 17 | 8 |

**高級非託管虛擬機器磁片：每個 VM 限制**

| 資源 | 限制 |
| --- | --- |
| 每個 VM 的最大 IOPS |80,000 IOPS (使用 GS5 虛擬機器) |
| 每個 VM 的最大輸送量 |使用 GS5 VM 時，2，000 MB/秒 |

