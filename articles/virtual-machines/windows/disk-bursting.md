---
title: 受控磁碟高載
description: 了解適用於 Azure 磁碟的磁碟高載和適用於 Azure 虛擬機器的磁碟高載
author: albecker1
ms.author: albecker
ms.date: 04/27/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 25aa9fc166e831acd2ed0389bbbe4d2dc7e04b19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82594373"
---
# <a name="disk-bursting"></a>磁碟高載
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虛擬機器層級高載
公用雲端中的所有區域已針對下列支援的規模啟用 VM 層級高載支援： 
- [Lsv2 系列](../lsv2-series.md)

針對支援的虛擬機器，預設會啟用高載。

## <a name="disk-level-bursting"></a>磁碟層級高載
高載也適用于我們的[Premium ssd](disks-types.md#premium-ssd) ，適用于所有區域中 P20 和較小的磁片大小。 在支援的新部署磁碟大小中，依預設會啟用磁碟高載。 現有的磁碟大小如果支援磁碟高載，可以透過下列其中一種方法來啟用高載： 
- **重新啟動 VM** 
- **中斷連結並重新連結磁碟**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
