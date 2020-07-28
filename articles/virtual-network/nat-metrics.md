---
title: Azure 虛擬網路 NAT 的計量和警示
titleSuffix: Azure Virtual Network
description: 了解適用於虛擬網路 NAT 的 Azure 監視器計量和警示。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 75e20a4fb91b73bc353d347f0b34f9be8f7a8a58
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84709941"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure 虛擬網路 NAT 計量

Azure 虛擬網路 NAT 閘道資源可提供多維度計量。 您可以使用這些計量來觀察作業，以供進行[疑難排解](troubleshoot-nat.md)。  您可以針對重大問題 (例如 SNAT 耗盡) 設定警示。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="可供輸出到網際網路的虛擬網路 NAT">
</p>

*圖：可供輸出到網際網路的虛擬網路 NAT*

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

* 了解[虛擬網路 NAT](nat-overview.md)
* 了解 [NAT 閘道資源](nat-gateway-resource.md)
* 了解 [Azure 監視器](../azure-monitor/overview.md)
* 了解[針對 NAT 閘道資源進行疑難排解](troubleshoot-nat.md)。
* [在 UserVoice 中告訴我們可為虛擬網路 NAT 打造的下一項功能](https://aka.ms/natuservoice)。


