---
title: 受控磁碟高載
description: 了解適用於 Azure 磁碟的磁碟高載和適用於 Azure 虛擬機器的磁碟高載
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: ab54b68ab3f7984ee18a39cf3a81fa663af54dee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90889103"
---
# <a name="disk-bursting"></a>磁碟高載
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虛擬機器層級高載
公用雲端中的所有區域已針對下列支援的規模啟用 VM 層級高載支援： 
- [Lsv2 系列](../lsv2-series.md)

VM 層級的高載也可在美國中西部中取得下列支援的大小：
- [Dsv3 系列](../dv3-dsv3-series.md)
- [Esv3 系列](../ev3-esv3-series.md)

針對支援的虛擬機器，預設會啟用高載。

## <a name="disk-level-bursting"></a>磁碟層級高載
高載也可在我們的 [Premium ssd](disks-types.md#premium-ssd) 上取得，適用于磁片大小 P20 且在所有區域中都較小。 在支援的新部署磁碟大小中，依預設會啟用磁碟高載。 現有的磁碟大小如果支援磁碟高載，可以透過下列其中一種方法來啟用高載： 
- **重新啟動 VM** 
- **中斷連結並重新連結磁碟**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
