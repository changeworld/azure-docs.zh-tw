---
title: 什麼是安全虛擬中樞？
description: 了解安全虛擬中樞
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: victorh
ms.openlocfilehash: d93c3b47c518962bf45762b846eaf53e490ae8f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "73518433"
---
# <a name="what-is-a-secured-virtual-hub"></a>什麼是安全虛擬中樞？

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

虛擬中心是 Microsoft 管理的虛擬網路，支援從其他資源進行連接。 從 Azure 門戶中的虛擬 WAN 創建虛擬中心時，將創建虛擬中心 VNet 和閘道（可選）。

*安全*虛擬中心是[Azure 虛擬廣域網路中心](../virtual-wan/virtual-wan-about.md#resources)，具有 Azure 防火牆管理器配置的關聯安全和路由策略。 使用安全的虛擬集線器輕鬆創建具有本地安全服務的集線器和傳輸式體系結構，以便進行流量治理和保護。 

您可以將安全的虛擬中心用作託管中央 VNet，無需預連接。 它取代了以前 Azure 防火牆部署所需的中央 VNet。 由於安全的虛擬集線器提供自動路由，因此無需配置自己的 UDR（使用者定義的路由）來路由通過防火牆的流量。

也可以將安全的虛擬集線器用作完整虛擬 WAN 體系結構的一部分。 此體系結構提供到 Azure 並通過 Azure 的安全、優化和自動化分支連接。 您可以選擇用於保護和管理網路流量的服務，包括 Azure 防火牆和其他協力廠商安全作為服務 （SECaaS） 供應商。

## <a name="create-a-secured-virtual-hub"></a>創建安全的虛擬中心

在 Azure 門戶中使用防火牆管理器，可以創建新的安全虛擬中心，也可以轉換以前使用 Azure 虛擬 WAN 創建的現有虛擬中心。

## <a name="next-steps"></a>後續步驟

要創建安全的虛擬中心並使用它來保護和管理集線器和分支網路，請參閱[教程：使用 Azure 門戶使用 Azure 防火牆管理器保護雲網路](secure-cloud-network.md)。