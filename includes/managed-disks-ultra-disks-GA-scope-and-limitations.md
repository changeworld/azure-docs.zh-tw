---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: aa9327bd0ba6763aa4e89630611aabb3c5195655
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85448704"
---
目前，ultra 磁片有額外的限制，如下所示：

目前僅適用于 ultra 磁片的基礎結構冗余選項是「可用性區域」。 使用任何其他冗余選項的 Vm 無法連接 ultra 磁片。

下表概述可使用 ultra 磁片的區域，以及其對應的可用性選項：

> [!NOTE]
> 如果下列清單中的區域沒有具備 ultra 磁片功能的可用性區域，則必須部署該區域中的 Vm，而不需要任何基礎結構重複選項，才能連接 ultra 磁片。

|區域  |支援 ultra 磁片的可用性區域數目  |
|---------|---------|
|US Gov 維吉尼亞州     |None         |
|美國中南部     |None         |
|美國中部     |三個區域         |
|美國西部     |None         |
|美國西部 2    |三個區域         |
|美國東部     |三個區域         |
|美國東部 2     |兩個區域         |
|東南亞     |三個區域         |
|北歐     |三個區域          |
|西歐     |三個區域          |
|英國南部     |三個區域          |
|日本東部     |兩個區域         |
|法國中部    |兩個區域        |


- 僅支援下列 VM 系列：
    - [ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series)
    - [DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series)
    - [FSv2](../articles/virtual-machines/fsv2-series.md)
    - [LSv2](../articles/virtual-machines/lsv2-series.md)
    - [M](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
    - [Mv2](../articles/virtual-machines/workloads/sap/hana-vm-operations-storage.md)
- 並非每個 VM 大小都適用于具有 ultra 磁片的每個支援區域
- 僅適用于資料磁片，而且只支援4k 實體磁區大小。 由於 Ultra 磁片的4K 原生磁區大小，有些應用程式無法與 ultra 磁片相容。 其中一個範例會 Oracle Database，這需要12.2 版或更新版本才能支援 ultra 磁片。  
- 只能建立為空磁碟  
- 目前不支援磁片快照集、VM 映射、可用性設定組、Azure 專用主機或 Azure 磁片加密
- 目前不支援與 Azure 備份或 Azure Site Recovery 的整合
- 僅支援非快取的讀取和非快取寫入
- GA Vm 上目前的 IOPS 上限為80000。

根據預設，每個訂用帳戶每個區域最多可提供16個 TiB，但 ultra 磁片會依要求支援更高的容量。 若要要求增加容量，請聯絡 Azure 支援。