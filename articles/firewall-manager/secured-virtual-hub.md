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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563626"
---
# <a name="what-is-a-secured-virtual-hub"></a>什麼是安全虛擬中樞？

虛擬中樞是受 Microsoft 管理的虛擬網路，可讓您從其他資源連線。 從 Azure 入口網站中的虛擬 WAN 建立虛擬中樞時，會建立虛擬中樞 VNet 和閘道（選擇性）作為其元件。

*受保護*的虛擬中樞是[Azure 虛擬 WAN 中樞](../virtual-wan/virtual-wan-about.md#resources)，具有 azure 防火牆管理員所設定的相關安全性和路由原則。 使用受保護的虛擬中樞，輕鬆建立具有原生安全性服務的中樞和輪輻和可轉移架構，以用於流量治理和保護。 

您可以使用受保護的虛擬中樞做為受管理的中央 VNet，而不會有任何內部部署連線能力。 它會取代先前 Azure 防火牆部署所需的中央 VNet。 由於受保護的虛擬中樞會提供自動路由，因此不需要設定您自己的 Udr （使用者定義的路由），即可透過防火牆路由傳送流量。

您也可以使用受保護的虛擬中樞作為完整虛擬 WAN 架構的一部分。 此架構提供與 Azure 之間的安全、優化且自動分支連線能力。 您可以選擇服務來保護和管理您的網路流量，包括 Azure 防火牆和其他協力廠商安全性即服務（SECaaS）提供者。

## <a name="create-a-secured-virtual-hub"></a>建立安全虛擬中樞

在 Azure 入口網站中使用防火牆管理員，您可以建立新的安全虛擬中樞，或轉換您先前使用 Azure 虛擬 WAN 建立的現有虛擬中樞。

## <a name="next-steps"></a>後續步驟

若要建立受保護的虛擬中樞，並使用它來保護和管理中樞和輪輻網路，請參閱[教學課程：使用 Azure 入口網站以 Azure 防火牆管理員保護您的雲端網路](secure-cloud-network.md)。