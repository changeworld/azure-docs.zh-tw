---
title: Azure 中的 Load Balancer 閒置 TCP 重設
titleSuffix: Azure Load Balancer
description: 在本文中，您將瞭解在閒置超時時，使用雙向 TCP RST 封包的 Azure Load Balancer。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2019
ms.author: allensu
ms.openlocfilehash: 68714053ac92faf8550a3e5f83a526afa1222971
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84808468"
---
# <a name="load-balancer-with-tcp-reset-on-idle"></a>具有閒置 TCP 重設的 Load Balancer

您可以使用 [Standard Load Balancer](load-balancer-standard-overview.md)，藉由對某個指定規則啟用「閒置時重設 TCP」，來為您的案例建立更容易預測的應用程式行為。 Load Balancer 的預設行為是在達到流程的閒置逾時時，以無訊息模式卸除流程。  啟用此功能會讓 Load Balancer 在閒置逾時的時候，傳送雙向的 TCP 重設 (TCP RST 封包)。  這會讓您的應用程式端點知道，連線已逾時且無法再供使用。  如有需要，端點可以立即建立新的連線。

![負載平衡器 TCP 重設](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
您變更此預設行為，以及對輸入 NAT 規則、負載平衡規則和[輸出規則](https://aka.ms/lboutboundrules)啟用傳送閒置逾時 TCP 重設。  根據規則啟用時，Load Balancer 會在達到所有相符流程的閒置逾時時，將雙向 TCP 重設 (TCP RST 封包) 傳送至用戶端和伺服器端點。

收到 TCP RST 封包的端點會立即關閉對應的通訊端。 這會立即通知端點有關已經發生連線的版本，而且相同 TCP 連線上的任何未來通訊都會失敗。  如果通訊端視需要關閉並重新建立連線，而不需要等到 TCP 連線最後逾時，則應用程式可以清除連線。

在許多情況下，這可能不需要傳送 TCP (或應用程式層) 保持運作，即可重新整理流程的閒置逾時。 

如果閒置持續時間超過設定所允許的值，或您的應用程式顯示已啟用 TCP 重設的非預期行為，您可能仍然需要使用 TCP Keepalive (或應用程式層 Keepalive) 來監視 TCP 連線的作用性。  此外，Keepalive 仍然適用於在路徑的某處 Proxy 處理連線時，特別是應用程式層 Keepalive。  

請仔細檢查整個完整情節，決定您是否受益於啟用 TCP 重設、調整閒置逾時，以及可能需要額外的步驟，以確保所需的應用程式行為。

## <a name="enabling-tcp-reset-on-idle-timeout"></a>啟用閒置逾時的 TCP 重設

使用 API 版本 2018-07-01，您可以啟用傳送每個規則的閒置逾時雙向 TCP 重設：

```json
      "loadBalancingRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "inboundNatRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

```json
      "outboundRules": [
        {
          "enableTcpReset": true | false,
        }
      ]
```

## <a name="region-availability"></a><a name="regions"></a>區域可用性

所有區域都有提供。

## <a name="limitations"></a>限制

- TCP RST 只會在已建立狀態的 TCP 連線期間傳送。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[Standard Load Balancer](load-balancer-standard-overview.md)。
- 深入瞭解[輸出規則](load-balancer-outbound-rules-overview.md)。
- [設定閒置超時的 TCP RST](load-balancer-tcp-idle-timeout.md)
