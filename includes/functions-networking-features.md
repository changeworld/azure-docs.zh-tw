---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578808"
---


| 功能 |[取用方案](../articles/azure-functions/functions-scale.md#consumption-plan)|[進階方案](../articles/azure-functions/functions-scale.md#premium-plan)|[專用方案](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[輸入 IP 限制和私人網站存取](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅是|✅是|✅是|✅是|✅是|
|[虛擬網路整合](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌否|✅是 (區域性)|✅是 (區域性和閘道)|✅是| ✅是|
|[虛擬網路觸發程序 (非 HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌否| ✅是 |✅是|✅是|✅是|
|[混合式連線](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (僅限 Windows)|❌否|✅是|✅是|✅是|✅是|
|[輸出 IP 限制](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌否| ✅是|✅是|✅是|✅是|