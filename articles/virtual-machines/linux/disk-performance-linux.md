---
title: 虛擬機器和磁碟效能
description: 深入瞭解 Vm 及其連接的磁片如何結合效能
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 092368bb66784a00d5116da0b6be6513f8ebb261
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518045"
---
# <a name="virtual-machine-and-disk-performance"></a>虛擬機器和磁碟效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
針對 premium 儲存體和 premium 儲存體快取啟用的虛擬機器有兩個不同的儲存體頻寬限制。 讓我們看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 和 Standard_D8s_v3 的相關檔：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

讓我們在建立 IO 活動的此虛擬機器和磁片組合上執行基準測試。 若要瞭解如何在 Azure 上對儲存體 IO 進行基準測試，請參閱 [Azure 磁碟儲存體上的應用程式基準測試](disks-benchmarks.md)。 從效能評定工具中，您可以看到 VM 和磁片組合可以達到 22800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
