---
title: 什麼是安全虛擬中樞？
description: 了解安全虛擬中樞
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 56e0d40bcbfb97f57b63dc82da1a6604f83dffad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85563626"
---
# <a name="what-is-a-secured-virtual-hub"></a>什麼是安全虛擬中樞？

虛擬中樞是受 Microsoft 管理的虛擬網路，可讓您從其他資源連接。 從 Azure 入口網站中的虛擬 WAN 建立虛擬中樞時，系統會將虛擬中樞 VNet 和閘道 (選擇性的) 建立為其元件。

*受保護*的虛擬中樞是[Azure 虛擬 WAN 中樞](../virtual-wan/virtual-wan-about.md#resources)，具有 Azure 防火牆管理員設定的相關聯安全性和路由原則。 使用安全的虛擬中樞，利用原生安全性服務，輕鬆建立中樞和輪輻和可轉移的架構，以進行流量治理和保護。 

您可以使用安全的虛擬中樞做為不具內部部署連線能力的受控中央 VNet。 它會取代先前為 Azure 防火牆部署所需的中央 VNet。 因為安全的虛擬中樞提供自動路由，所以不需要設定您自己的 Udr (使用者定義的路由) 來透過防火牆路由傳送流量。

您也可以使用安全的虛擬中樞作為完整虛擬 WAN 架構的一部分。 此架構提供與 Azure 之間的安全、優化及自動化分支連線能力。 您可以選擇用來保護及管理網路流量的服務，包括 Azure 防火牆和其他協力廠商安全性即服務 (SECaaS) 提供者。

## <a name="create-a-secured-virtual-hub"></a>建立安全虛擬中樞

使用 Azure 入口網站中的防火牆管理員，您可以建立新的安全虛擬中樞，或轉換您先前使用 Azure 虛擬 WAN 建立的現有虛擬中樞。

## <a name="next-steps"></a>接下來的步驟

若要建立安全的虛擬中樞，並使用它來保護和控管中樞和輪輻網路，請參閱 [教學課程：使用 Azure 入口網站以 Azure 防火牆管理員保護您的雲端網路](secure-cloud-network.md)。