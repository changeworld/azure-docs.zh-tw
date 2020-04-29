---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a5e0e459800e7cb57672518597f3d04a74f53118
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81008621"
---
目前，ultra 磁片有額外的限制，如下所示：

目前僅適用于 ultra 磁片的基礎結構冗余選項是「可用性區域」。 使用任何其他冗余選項的 Vm 無法連接 ultra 磁片。

下表概述可使用 ultra 磁片的區域，以及其對應的可用性選項：

> [!NOTE]
> 這些區域內的部分可用性區域不提供 ultra 磁片。

|區域  |沒有基礎結構冗余  |可用性區域  |
|---------|---------|---------|
|美國西部     |是         |否         |
|美國西部 2    |否         |是         |
|美國東部     |否         |是         |
|美國東部 2     |否         |是         |
|東南亞     |否         |是         |
|北歐     |否         |是         |
|西歐     |否         |是         |
|英國南部     |否         |是         |

- 僅支援下列 VM 系列：
    - [ESv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - [DSv3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/)
    - FSv2
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非每個 VM 大小都適用于具有 ultra 磁片的每個支援區域
- 僅適用于資料磁片，而且只支援4k 實體磁區大小。 由於 Ultra 磁片的4K 原生磁區大小，有些應用程式無法與 ultra 磁片相容。 其中一個範例會 Oracle Database，這需要12.2 版或更新版本才能支援 ultra 磁片。  
- 只能建立為空磁碟  
- 目前不支援磁片快照集、VM 映射、可用性設定組、Azure 專用主機或 Azure 磁片加密
- 目前不支援與 Azure 備份或 Azure Site Recovery 的整合
- GA Vm 上目前的 IOPS 上限為80000。

根據預設，每個訂用帳戶每個區域最多可提供16個 TiB，但 ultra 磁片會依要求支援更高的容量。 若要要求增加容量，請聯絡 Azure 支援。