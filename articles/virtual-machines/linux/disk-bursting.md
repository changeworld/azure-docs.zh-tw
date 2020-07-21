---
title: 受控磁碟高載
description: 了解適用於 Azure 磁碟的磁碟高載和適用於 Azure 虛擬機器的磁碟高載
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4408b7d442ce3f4fbb1ea61031295375ae036493
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/20/2020
ms.locfileid: "86494542"
---
# <a name="managed-disk-bursting"></a>受控磁碟高載
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>虛擬機器層級高載
公用雲端中的所有區域已針對下列支援的規模啟用 VM 層級高載支援： 
- [Lsv2 系列](../lsv2-series.md)

針對支援的虛擬機器，預設會啟用高載。

## <a name="disk-level-bursting"></a>磁碟層級高載
在 Azure 公用、政府和中國雲端的所有區域中，我們的[進階 SSD](disks-types.md#premium-ssd) 也提供高載，適用於 P20 和更小的磁碟大小。 預設會在支援磁片大小的所有全新和現有部署上啟用磁片負載平衡。 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
