---
title: 虛擬機器和磁片效能
description: 深入瞭解 Vm 及其連接的磁片如何結合效能
author: albecker1
ms.author: albecker
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6621efe6d0b391ed38ab49f147d791a11db14301
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89664097"
---
# <a name="virtual-machine-and-disk-performance"></a>虛擬機器和磁片效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
啟用高階儲存體且啟用 premium 儲存體快取的虛擬機器有兩個不同的儲存體頻寬限制。 讓我們繼續看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 的檔，Standard_D8s_v3：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
