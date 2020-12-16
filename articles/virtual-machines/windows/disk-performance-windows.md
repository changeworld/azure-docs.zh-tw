---
title: 虛擬機器和磁片效能-Windows
description: 深入瞭解虛擬機器及其連接的磁片在 Windows 上的效能組合如何運作。
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: dec8b0cc33a9fffa7cac1ac9261b3c38ef5a6449
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584114"
---
# <a name="virtual-machine-and-disk-performance-windows"></a> (Windows) 的虛擬機器和磁片效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
 啟用 premium 儲存體且啟用高階儲存體快取的虛擬機器，有兩個不同的儲存體頻寬限制。 讓我們繼續看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 的檔，Standard_D8s_v3：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

讓我們對此 VM 和磁片組合執行基準測試，以建立 IO 活動，您可以在 [這裡](disks-benchmarks.md)瞭解如何在 Azure 上對儲存體 IO 進行基準測試。 從效能評定工具中，您可以看到 VM 和磁片組合能夠達到 22800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]