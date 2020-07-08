---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "67173932"
---
請務必了解有兩種方式可以在 Azure 中設定可用性群組接聽程式。 這些方法的差異在於您建立接聽程式時使用的 Azure Load Balancer 類型。 下表描述其差異：

| 負載平衡器類型 | 實作 | 使用時機： |
| --- | --- | --- |
| **外設** |使用主控虛擬機器 (VM) 之雲端服務的 [公用虛擬 IP 位址]**。 |您需要從虛擬網路外部存取接聽程式，包括從網際網路。 |
| **內部** |使用「內部負載平衡」** 搭配接聽程式的私用位址。 |您只能從相同的虛擬網路內存取接聽程式。 此存取包括混合案例中的站對站 VPN。 |

> [!IMPORTANT]
> 對於使用雲端服務公用 VIP 的接聽程式 (外部負載平衡器)，只要用戶端、接聽程式和資料庫位於相同的 Azure 區域中，就不會向您收取輸出流量費用。 否則，透過接聽程式傳回的所有資料都會被視為輸出流量，並以正常資料傳輸率向您收費。 
> 
> 

ILB 只可以在具有區域範圍的虛擬網路上設定。 已針對同質群組設定的現有虛擬網路無法使用 ILB。 如需詳細資訊，請參閱[內部負載平衡器概觀](../articles/load-balancer/load-balancer-internal-overview.md)。

