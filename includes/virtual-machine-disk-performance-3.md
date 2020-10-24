---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518043"
---
![顯示 r = 22.8 k 醒目提示之 f i o 輸出輸出的螢幕擷取畫面。](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 可以達到總 28600 IOPS。 使用計量，讓我們調查發生什麼事，並找出儲存體 IO 瓶頸。 在左窗格中，選取 [ **計量**：

![顯示在左窗格中反白顯示計量的螢幕擷取畫面。](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

首先，讓我們看看 VM 快取的 **IOPS 耗用百分比** 度量：

![螢幕擷取畫面，顯示 V M 快取的 I O P S 已耗用百分比。](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

此計量會告訴我們，已將61% 的 16000 IOPS 分配給 VM 上的快取 IOPS 正在使用中。 此百分比表示儲存體 IO 瓶頸與快取的磁片無關，因為它不是100%。 現在讓我們看看 VM 未快取的 **IOPS 百分比** 度量：

![螢幕擷取畫面，顯示 V M 未快取的 I O P S 已耗用百分比。](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

此度量為100%。 它會告訴我們，所有分配給 VM 上未快取 IOPS 的 12800 IOPS 都在使用中。 我們可以補救此問題的其中一種方式，是將 VM 的大小變更為較大的大小，以便可以處理額外的 IO。 但在這麼做之前，讓我們先看看連接的磁片，以找出它們所看到的 IOPS 數目。 查看作業系統磁片 **IOPS 耗用百分比**來檢查作業系統磁片：

![螢幕擷取畫面，顯示 O S 磁片 I O P S 已耗用百分比。](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

此計量告訴我們，針對此 P30 OS 磁片所布建的 5000 IOPS 大約90% 正在使用中。 此百分比表示 OS 磁片上沒有瓶頸。 現在讓我們查看已連結至 VM 的資料磁片，方法是查看所 **耗用的資料磁片 IOPS 百分比**：

![顯示資料磁片 I O P S 已耗用百分比的螢幕擷取畫面。](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

此計量會告訴我們所有附加磁片的平均 IOPS 耗用百分比大約是42%。 此百分比是根據磁片所使用的 IOPS 來計算，而不是從主機快取提供。 讓我們深入瞭解此計量，方法是對這些計量套用 *分割* ，並依 LUN 值進行分割：

![螢幕擷取畫面，顯示資料磁片 I O P S 已耗用分割的百分比。](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

此計量會告訴我們 LUN 3 和2上所連接的資料磁片，大約是使用其布建 IOPS 的85%。 以下是 IO 在 VM 和磁片架構中的外觀圖：

![儲存體 I O 計量範例的圖表。](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
