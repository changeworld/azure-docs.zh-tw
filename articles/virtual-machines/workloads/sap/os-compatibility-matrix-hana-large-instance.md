---
title: SAP Hana （大型實例）的作業系統相容性矩陣 |Microsoft Docs
description: 相容性矩陣代表不同作業系統版本與不同硬體類型（大型實例）的相容性
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675649"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>適用于 HANA 大型實例的相容作業系統

## <a name="hana-large-instance-type-i"></a>HANA 大型實例類型 I     
  | 作業系統 | 可用性        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 不再提供 | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP3      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm              |
  | SLES 12 SP4      | 可用           | S72、S72m、S96、S144、S144m、S192、S192m、S192xm、S224、S224m |
  
### <a name="persistent-memory-skus"></a>持續性記憶體 Sku
  | 作業系統 | 可用性 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 可用    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA 大型實例類型 II     
  |  作業系統       | 可用性        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | 不再提供 | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m |
  | SLES 12 SP3             | 可用           | S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm、S960m |
  
## <a name="related-documents"></a>相關文件

- 深入瞭解可用的[sku](hana-available-skus.md)
- 若要瞭解如何[升級作業系統](os-upgrade-hana-large-instance.md)
  

  
  
