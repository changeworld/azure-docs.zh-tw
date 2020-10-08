---
title: Load Balancer Azure 中的 TCP 重設和閒置超時
titleSuffix: Azure Load Balancer
description: 在本文中，您將瞭解在閒置 timeout 上使用雙向 TCP RST 封包 Azure Load Balancer。
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/07/2020
ms.author: allensu
ms.openlocfilehash: 060048bf786f424d5df6eb8fb4813877acb0fea0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823206"
---
# <a name="load-balancer-tcp-reset-and-idle-timeout"></a>Load Balancer TCP 重設和閒置超時

您可以使用 [Standard Load Balancer](load-balancer-standard-overview.md)，藉由對某個指定規則啟用「閒置時重設 TCP」，來為您的案例建立更容易預測的應用程式行為。 Load Balancer 的預設行為是在達到流程的閒置逾時時，以無訊息模式卸除流程。  啟用此功能會讓 Load Balancer 在閒置逾時的時候，傳送雙向的 TCP 重設 (TCP RST 封包)。  這會讓您的應用程式端點知道，連線已逾時且無法再供使用。  如有需要，端點可以立即建立新的連線。

![負載平衡器 TCP 重設](media/load-balancer-tcp-reset/load-balancer-tcp-reset.png)
 
## <a name="tcp-reset"></a>TCP 重設

您變更此預設行為，以及對輸入 NAT 規則、負載平衡規則和[輸出規則](https://aka.ms/lboutboundrules)啟用傳送閒置逾時 TCP 重設。  根據規則啟用時，Load Balancer 會在達到所有相符流程的閒置逾時時，將雙向 TCP 重設 (TCP RST 封包) 傳送至用戶端和伺服器端點。

收到 TCP RST 封包的端點會立即關閉對應的通訊端。 這會立即通知端點有關已經發生連線的版本，而且相同 TCP 連線上的任何未來通訊都會失敗。  如果通訊端視需要關閉並重新建立連線，而不需要等到 TCP 連線最後逾時，則應用程式可以清除連線。

在許多情況下，這可能不需要傳送 TCP (或應用程式層) 保持運作，即可重新整理流程的閒置逾時。 

如果閒置持續時間超過設定所允許的值，或您的應用程式顯示已啟用 TCP 重設的非預期行為，您可能仍然需要使用 TCP Keepalive (或應用程式層 Keepalive) 來監視 TCP 連線的作用性。  此外，Keepalive 仍然適用於在路徑的某處 Proxy 處理連線時，特別是應用程式層 Keepalive。  

請仔細檢查整個完整情節，決定您是否受益於啟用 TCP 重設、調整閒置逾時，以及可能需要額外的步驟，以確保所需的應用程式行為。

## <a name="configurable-tcp-idle-timeout"></a>可設定的 TCP 閒置逾時

Azure Load Balancer 具有下列閒置超時範圍：
-  輸出規則為4分鐘到100分鐘
-  針對 Load Balancer 規則和輸入 NAT 規則，4分鐘到30分鐘

根據預設，設定為 4 分鐘。 如果閒置期間超過逾時值，即無法保證仍能維持用戶端與雲端服務之間的 TCP 或 HTTP 工作階段。

當連線關閉時，您的用戶端應用程式可能會收到下列錯誤訊息：「基礎連線已關閉：應該保持運作的連接卻被伺服器關閉。」

常見作法是使用 TCP Keep-Alive。 此作法可讓連線保持長時間連線。 如需詳細資訊，請參閱這些 [.NET 文章](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)。 啟用 Keep-Alive 之後，就會在連線無活動期間傳送封包。 這些 Keep-Alive 封包可確保不會達到閒置逾時值，因此可以長期維持連線。

此設定僅適用於輸入連線。 若要避免連線中斷，請使用比閒置逾時設定短的間隔來設定 TCP Keep-Alive，或增加閒置逾時值。 為了支援這些情節，新增了可設定閒置逾時的支援。

TCP Keep-Alive 適用於電池使用時間不受約束的情節。 不建議用於行動應用程式。 在行動裝置應用程式中使用 TCP Keep-Alive 可能會更快耗盡裝置電池電力。


## <a name="limitations"></a>限制

- TCP 重設只會在 TCP 連接處於已建立狀態時傳送。
- 設定 HA 埠的內部負載平衡器不會傳送 TCP 重設。
- TCP 閒置超時不會影響 UDP 通訊協定上的負載平衡規則。

## <a name="next-steps"></a>下一步

- 瞭解 [Standard Load Balancer](load-balancer-standard-overview.md)。
- 深入瞭解 [輸出規則](load-balancer-outbound-rules-overview.md)。
- [設定閒置 Timeout 的 TCP RST](load-balancer-tcp-idle-timeout.md)
