---
title: Azure Load Balancer 浮動 IP 設定
description: Azure Load Balancer 浮動 IP 的總覽
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699401"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Azure Load Balancer 浮動 IP 設定

負載平衡器為 UDP 和 TCP 應用程式提供數種功能。

## <a name="floating-ip"></a>浮動 IP

在某些應用程式案例中，後端集區中單一 VM 上的多個應用程式執行個體偏好或必須使用相同連接埠。 連接埠重複使用的常見範例包括提供高可用性的叢集、網路虛擬裝置、公開多個不會重新加密的 TLS 端點。 如果您想要在多個規則重複使用後端連接埠，必須啟用規則定義中的浮動 IP。

**浮動 IP** 是 Azure 術語，屬於伺服器直接回傳 (DSR) 的一部分。 DSR 包括兩個部分：

- 流程拓撲
- IP 位址對應配置

在平台層級，Azure Load Balancer 一律在 DSR 流程拓撲中運作，無論是否已啟用浮動 IP。 這表示流程的輸出部分一律會正確重寫為直接流回原始來源。
若沒有浮動 IP，Azure 會公開傳統負載平衡 IP 位址對應配置以利使用的方便性 (VM 執行個體的 IP)。 啟用浮動 IP 會將 IP 位址對應變更為負載平衡器的前端 IP，以提供更大的彈性。 [在此](load-balancer-multivip-overview.md)深入了解。

## <a name="limitations"></a><a name = "limitations"></a>限制

- 負載平衡案例的次要 IP 設定上目前不支援浮動 IP

## <a name="next-steps"></a>後續步驟

- 請參閱 [建立公用 Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) 以開始使用 Load Balancer。
- 深入了解 [Azure Load Balancer 輸出連線](load-balancer-outbound-connections.md)。
- 深入了解 [Azure Load Balancer](load-balancer-overview.md)。
- 深入了解[健康情況探查](load-balancer-custom-probe-overview.md)。
- 了解[標準 Load Balancer 診斷](load-balancer-standard-diagnostics.md)。
- 深入了解[網路安全性群組](../virtual-network/network-security-groups-overview.md)。