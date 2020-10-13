---
title: 什麼是安全虛擬中樞？
description: 了解安全虛擬中樞
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: victorh
ms.openlocfilehash: c840bf9e82b8dcdb1fbf9b380ea847b3d1b08dd9
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948063"
---
# <a name="what-is-a-secured-virtual-hub"></a>什麼是安全虛擬中樞？

虛擬中樞是受 Microsoft 管理的虛擬網路，可讓您從其他資源連接。 從 Azure 入口網站中的虛擬 WAN 建立虛擬中樞時，系統會將虛擬中樞 VNet 和閘道 (選擇性的) 建立為其元件。

*受保護*的虛擬中樞是[Azure 虛擬 WAN 中樞](../virtual-wan/virtual-wan-about.md#resources)，具有 Azure 防火牆管理員設定的相關聯安全性和路由原則。 使用安全的虛擬中樞，利用原生安全性服務，輕鬆建立中樞和輪輻和可轉移的架構，以進行流量治理和保護。 

您可以使用安全的虛擬中樞來篩選虛擬網路之間的流量 (V2V) 、虛擬網路和分公司 (B2V) 以及網際網路的流量 (B2I/V2I) 。 安全的虛擬中樞會提供自動路由。 您不需要設定自己的 Udr (使用者定義的路由) 來透過防火牆來路由傳送流量。

您可以選擇所需的安全性提供者來保護及管理您的網路流量，包括 Azure 防火牆、協力廠商安全性即服務 (SECaaS) 提供者，或兩者。 目前，安全中樞不支援分支對多個中樞進行 (B2B) 篩選和篩選。 若要深入瞭解，請參閱 [什麼是 Azure 防火牆管理員？](overview.md#known-issues)。 

## <a name="create-a-secured-virtual-hub"></a>建立安全虛擬中樞

使用 Azure 入口網站中的防火牆管理員，您可以建立新的安全虛擬中樞，或轉換您先前使用 Azure 虛擬 WAN 建立的現有虛擬中樞。

## <a name="next-steps"></a>後續步驟

若要建立安全的虛擬中樞，並使用它來保護和控管中樞和輪輻網路，請參閱 [教學課程：使用 Azure 入口網站以 Azure 防火牆管理員保護您的雲端網路](secure-cloud-network.md)。