---
title: 虛擬機器和磁碟效能
description: 深入瞭解 Vm 及其連接的磁片如何結合效能
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 3b849640bc7adb38fe51c3c7a9dda2d1d14f35fa
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017245"
---
# <a name="virtual-machine-and-disk-performance"></a>虛擬機器和磁碟效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
啟用高階儲存體且啟用 premium 儲存體快取的虛擬機器有兩個不同的儲存體頻寬限制。 讓我們繼續看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 的檔，Standard_D8s_v3：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

讓我們對此 VM 和磁片組合執行基準測試，以建立 IO 活動，您可以在 [這裡](disks-benchmarks.md)瞭解如何在 Azure 上對儲存體 IO 進行基準測試。 從效能評定工具中，您可以看到 VM 和磁片組合能夠達到 22800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]