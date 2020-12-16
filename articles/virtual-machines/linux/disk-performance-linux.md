---
title: 虛擬機器和磁片效能-Linux
description: 深入瞭解虛擬機器及其連接的磁片如何結合以在 Linux 上使用效能。
author: albecker1
ms.author: albecker
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 2c48672bcfd8c552b36e3ae0807135924669c1d9
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591849"
---
# <a name="virtual-machine-and-disk-performance-linux"></a> (Linux) 的虛擬機器和磁片效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
針對 premium 儲存體和 premium 儲存體快取啟用的虛擬機器有兩個不同的儲存體頻寬限制。 讓我們看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 和 Standard_D8s_v3 的相關檔：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]

讓我們在建立 IO 活動的此虛擬機器和磁片組合上執行基準測試。 若要瞭解如何在 Azure 上對儲存體 IO 進行基準測試，請參閱 [Azure 磁碟儲存體上的應用程式基準測試](disks-benchmarks.md)。 從效能評定工具中，您可以看到 VM 和磁片組合可以達到 22800 IOPS：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-3.md)]
