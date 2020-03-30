---
title: Azure 防火牆集中管理
description: 瞭解 Azure 防火牆管理器集中管理
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 58f670f3f55a63f0c1823adc13c98f5863d4d650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444546"
---
# <a name="azure-firewall-central-management"></a>Azure 防火牆集中管理

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

如果您管理多個防火牆，您知道不斷更改的防火牆規則會使它們保持同步變得困難。中央 IT 團隊需要一種方法來定義基本防火牆策略，並在多個營業單位中強制實施這些策略。 同時，DevOps 團隊希望創建自己的本地派生防火牆策略，以取得更好的敏捷性。

Azure 防火牆管理器預覽可説明解決這些問題。


## <a name="azure-firewall-manager-preview"></a>Azure 防火牆管理器預覽

Azure 防火牆管理器預覽版是一種網路安全管理服務，為基於雲的安全週邊提供中央安全性原則和路由管理。 它使企業 IT 團隊能夠輕鬆集中定義網路和應用程式級別規則，以便跨多個 Azure 防火牆實例進行流量篩選。 您可以在集線器和分支體系結構中跨越不同的 Azure 區域和訂閱，以便進行流量治理和保護。 它還通過跨組織實現的派生本地防火牆安全性原則為 DevOps 提供更好的敏捷性。

### <a name="firewall-policy"></a>防火牆策略

防火牆策略是包含 NAT、網路和應用程式規則集合以及威脅智慧設置的 Azure 資源。 它是一個全域資源，可用於*安全虛擬中心*和*集線器虛擬網路中*的多個 Azure 防火牆實例。 可以從頭開始創建新策略，也可以從現有策略繼承。 繼承允許 DevOps 在組織授權的基礎策略之上創建本地防火牆策略。 策略跨區域和訂閱工作。
 
您可以創建防火牆策略和與 Azure 防火牆管理器的關聯。 但是，您還可以使用 REST API、範本、Azure PowerShell 和 CLI 創建和管理原則。 創建策略後，可以將其與虛擬 WAN 集線器中的防火牆關聯，使其成為*安全虛擬集線器*和/或虛擬網路中的防火牆，使其成為*集線器虛擬網路*。

### <a name="pricing"></a>定價

策略根據防火牆關聯計費。 具有零或一個防火牆關聯的策略是免費的。 具有多個防火牆關聯的策略以固定費率計費。 有關詳細資訊，請參閱[Azure 防火牆管理器定價](https://azure.microsoft.com/pricing/details/firewall-manager/)。

## <a name="azure-firewall-management-partners"></a>Azure 防火牆管理合作夥伴

以下領先的協力廠商解決方案支援使用標準 Azure REST API 進行 Azure 防火牆集中管理。 每個解決方案都有其獨特的特點和特點：

- [阿爾戈塞雲流](https://www.algosec.com/azure/) 
- [梭子魚雲安全衛士](https://www.barracuda.com/products/cloudsecurityguardian/for_azure)
- [圖芬·奧卡](https://www.tufin.com/products/tufin-orca)


## <a name="next-steps"></a>後續步驟

有關 Azure 防火牆管理器預覽的詳細資訊，請參閱[什麼是 Azure 防火牆管理器預覽？](../firewall-manager/overview.md)