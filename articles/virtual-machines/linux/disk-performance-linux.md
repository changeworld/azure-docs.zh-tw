---
title: 虛擬機器和磁碟效能
description: 深入瞭解 Vm 及其連接的磁片如何結合效能
author: albecker1
ms.author: albecker
ms.date: 09/25/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: c248549fae9e5a0b80f70223a2a1a1f9c7ac9c81
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541521"
---
# <a name="virtual-machine-and-disk-performance"></a>虛擬機器和磁碟效能
[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance.md)]

## <a name="virtual-machine-uncached-vs-cached-limits"></a>虛擬機器未快取與快取限制
啟用高階儲存體且啟用 premium 儲存體快取的虛擬機器有兩個不同的儲存體頻寬限制。 讓我們繼續看看 Standard_D8s_v3 的虛擬機器作為範例。 以下是 [Dsv3 系列](../dv3-dsv3-series.md) 的檔，Standard_D8s_v3：

[!INCLUDE [VM and Disk Performance](../../../includes/virtual-machine-disk-performance-2.md)]
