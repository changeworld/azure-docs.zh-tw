---
title: Azure 虛擬網路 NAT 的計量和警示
titleSuffix: Azure Virtual Network
description: 了解適用於虛擬網路 NAT 的 Azure 監視器計量和警示。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 268f5f5b7bf1bed27a01085974bea72af6a84f3b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304050"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure 虛擬網路 NAT 計量

Azure 虛擬網路 NAT 閘道資源可提供多維度計量。 您可以使用這些計量來觀察作業，以供進行[疑難排解](nat-metrics.md)。  您可以針對重大問題 (例如 SNAT 耗盡) 設定警示。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="可供輸出到網際網路的虛擬網路 NAT">
</p>

*圖：可供輸出到網際網路的虛擬網路 NAT*

>[!NOTE] 
>虛擬網路 NAT 目前可作為公開預覽版提供。 其目前僅適用於一組有限的[區域](nat-overview.md#region-availability)。 此預覽版是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽專用的補充使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms)。

## <a name="metrics"></a>計量

NAT 閘道資源會在 Azure 監視器中提供下列多維度計量：

| 計量 | 描述 | 建議的彙總 | 維度 |
|---|---|---|---|
| 位元組 | 已處理的輸入和輸出位元組數 | Sum | 方向 (In；Out)、通訊協定 (6 TCP；17 UDP) |
| 封包 | 已處理的輸入和輸出的封包數 | Sum | 方向 (In；Out)、通訊協定 (6 TCP；17 UDP) |
| 丟棄的封包 | NAT 閘道丟棄的封包 | Sum | / |
| SNAT 連線計數 | 每個間隔的狀態轉換 | Sum | 連線狀態，通訊協定 (6 TCP；17 UDP) |
| SNAT 連線總計數 | 目前作用中的 SNAT 連線 (~使用中的 SNAT 連接埠) | Sum | 通訊協定 (6 TCP；17 UDP) |


## <a name="alerts"></a>警示

在 Azure 監視器中可以針對上述每個[計量](#metrics)設定計量的警示。

## <a name="limitations"></a>限制

不支援資源健康狀態。

## <a name="next-steps"></a>後續步驟

- 了解[虛擬網路 NAT](nat-overview.md)
- 了解 [NAT 閘道資源](nat-gateway-resource.md)
- 了解 [Azure 監視器](../azure-monitor/overview.md)
- 了解[針對 NAT 閘道資源進行疑難排解](troubleshoot-nat.md)。

