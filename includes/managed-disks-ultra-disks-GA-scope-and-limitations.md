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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74935955"
---
目前，超磁片有額外的限制，如下所示：

- 支援以下區域，每個區域的可用性區域數量不同：
    - 美國東部 2
    - 美國東部
    - 美國西部 2
    - 東南亞
    - 北歐
    - 西歐
    - 英國南部 
- 只能用於可用性區域 (區域以外的可用性設定組和單一 VM 部署將無法連結 Ultra磁碟)
- 僅在以下 VM 系列中受支援：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非所有 VM 大小都可用於每個支援的區域，具有超磁片
- 僅作為資料磁片提供，僅支援 4k 物理磁區大小。 由於超磁片的 4K 本機磁區大小，有些應用程式與超磁片不相容。 例如，Oracle 資料庫需要版本 12.2 或更高版本才能支援超磁片。  
- 只能建立為空磁碟  
- 尚不支援磁片快照、VM 映射、可用性集和 Azure 磁片加密
- 尚不支援與 Azure 備份或 Azure 網站恢復集成
- GA VM 上的 IOPS 當前最大限制為 80，000。
- 如果您想參與 VM 的有限預覽，該預覽可以使用超磁片完成 160，000 個 IOPS，請發送電子郵件UltraDiskFeedback@microsoft.com