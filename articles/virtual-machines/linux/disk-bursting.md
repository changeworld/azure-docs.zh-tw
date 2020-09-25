---
title: 受控磁碟高載
description: 瞭解適用于 Linux 上 Azure 虛擬機器的 Azure 磁片與磁片高載的磁片負載平衡。
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 2e5fc8bde2c79a355fb7963c9101c4b040f97fa7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275146"
---
# <a name="managed-disk-bursting"></a>受控磁碟高載
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虛擬機器層級高載
公用雲端中的所有區域已針對下列支援的規模啟用 VM 層級高載支援： 
- [Lsv2 系列](../lsv2-series.md)

VM 層級的高載也可在美國中西部中取得下列支援的大小：
- [Dsv3 系列](../dv3-dsv3-series.md)
- [Esv3 系列](../ev3-esv3-series.md)

針對支援的虛擬機器，預設會啟用高載。

## <a name="disk-level-bursting"></a>磁碟層級高載
在 Azure 公用、政府和中國雲端的所有區域中，我們的[進階 SSD](disks-types.md#premium-ssd) 也提供高載，適用於 P20 和更小的磁碟大小。 在支援的磁片大小的所有新部署和現有部署上，預設會啟用磁片負載平衡。 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
