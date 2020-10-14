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
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016441"
---
![計量功能表](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

不過，Standard_D8s_v3 可以達到 28600 IOPs 的總計，使用計量可讓您調查發生什麼狀況，並找出儲存體 IO 瓶頸。 首先，找出左側功能表中的 [計量] 按鈕，然後選取它：

![計量功能表](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

首先，讓我們看看 VM 快取的 **IOPS 耗用百分比** 度量：

![VM 快取的 IOPS 耗用百分比](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

此計量會告知我們已將 16000 IOPs 分配給 VM 上的快取 IOPs，正在使用61%。 這表示儲存體 IO 瓶頸與快取的磁片不符，因為它不是100%。 現在讓我們看看 VM 未快取的 **IOPS 百分比** 度量：

![VM 未快取的已耗用 IOPS 百分比](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

此計量為100%，告知我們正在使用分配給 VM 上未快取 IOPs 的所有 12800 IOPs。 我們可以進行補救的其中一種方式，是將 VM 的大小變更為較大的大小，以便可以處理額外的 IO。 但是在這麼做之前，讓我們先看看連接的磁片，看看它們所看到的 IOPs 數目。 首先，讓我們看看作業系統磁片 **IOPS 耗用百分比**：

![作業系統磁片 IOPS 耗用百分比](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

此計量會告訴我們針對此 P30 OS 磁片布建的 5000 IOPs，大約是在使用中的90%。 這表示 OS 磁片上沒有瓶頸。 現在，讓我們查看已連結至 VM 的資料磁片，方法是查看所 **耗用的資料磁片 IOPS 百分比**：

![資料磁片 IOPS 耗用百分比](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

此計量告訴我們所有連接磁片的平均 IOPs 耗用百分比大約是42%。 此百分比的計算方式是根據磁片所使用的 IOPs，而不是從主機快取提供服務。 讓我們深入瞭解此計量，藉由在這些計量上套用 **分割** 並依 LUN 值分割來查看。

![分割的資料磁片 IOPS 耗用百分比](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

此計量會告知我們 LUN 3 和2上連接的資料磁片正在使用大約85% 的已布建 IOPs。 以下是 IO 在 VM 和磁片架構中的外觀圖：

![儲存體 IO 計量範例圖表](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
