---
title: 受控磁片高載
description: 瞭解適用于 Azure 磁片的磁片負載平衡和 Azure 虛擬機器的磁片負載平衡
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594741"
---
# <a name="disk-bursting"></a>磁碟高載
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虛擬機器層級負載平衡
在公用雲端中的所有區域中，會針對這些支援的大小啟用 VM 層級的高載支援： 
- [Lsv2 系列](../lsv2-series.md)

針對支援的虛擬機器，預設會啟用高載功能。

## <a name="disk-level-bursting"></a>磁片層級高載
高載也適用于我們的[Premium ssd](disks-types.md#premium-ssd) ，適用于所有區域中 P20 和較小的磁片大小。 預設會在支援的磁片大小的新部署上啟用磁片負載平衡。 現有的磁片大小如果支援磁片高載，可以透過下列其中一種方法來啟用高載： 
- **重新開機 VM** 
- **卸離並重新附加磁片**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
