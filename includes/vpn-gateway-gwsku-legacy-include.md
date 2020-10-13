---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "77211404"
---
舊版 (舊式) VPN 閘道 SKU 如下：

* 預設 (基本) 
* 標準
* 高效能

VPN 閘道不會使用 UltraPerformance 閘道 SKU。 如需 UltraPerformance SKU 的相關資訊，請參閱 [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md) 文件。

使用舊版 SKU 時，請考慮下列各項：

* 如果您想要使用原則式 VPN 類型，您必須使用基本 SKU。 其他所有 SKU 均不支援原則式 VPN (先前稱為靜態路由)。
* 基本 SKU 不支援 BGP。
* 基本 SKU 不支援 ExpressRoute-VPN 閘道共存組態。
* 僅可以在高效能 SKU 上設定主動-主動 S2S VPN 閘道連線。