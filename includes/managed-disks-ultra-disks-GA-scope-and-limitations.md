---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/18/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b819264895e35c6ef4fe9dc5263444dcac17eaa2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/08/2019
ms.locfileid: "74935955"
---
目前，ultra 磁片有額外的限制，如下所示：

- 在下欄區域中受到支援，而且每個區域的可用性區域數目各不相同：
    - 美國東部 2
    - 美國東部
    - 美國西部 2
    - 東南亞
    - 歐洲北部
    - 歐洲西部
    - 英國南部 
- 只能用於可用性區域 (區域以外的可用性設定組和單一 VM 部署將無法連結 Ultra磁碟)
- 僅支援下列 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非每個 VM 大小都適用于具有 ultra 磁片的每個支援區域
- 僅適用于資料磁片，而且只支援4k 實體磁區大小。 由於 Ultra 磁片的4K 原生磁區大小，有些應用程式無法與 ultra 磁片相容。 其中一個範例會 Oracle Database，這需要12.2 版或更新版本才能支援 ultra 磁片。  
- 只能建立為空磁碟  
- 尚不支援磁片快照集、VM 映射、可用性設定組和 Azure 磁片加密
- 尚不支援與 Azure 備份或 Azure Site Recovery 的整合
- GA Vm 上目前的 IOPS 上限為80000。
- 如果您想要參與可透過 ultra 磁片完成 160000 IOPS 的 VM 有限預覽，請 UltraDiskFeedback@microsoft .com 中的電子郵件