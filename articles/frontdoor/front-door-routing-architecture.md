---
title: Azure Front Door 路由架構 |Microsoft Docs
description: 此文章可協助您了解 Front Door 架構的全域檢視觀點。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91449191"
---
# <a name="routing-architecture-overview"></a>路由架構概觀

當 Azure Front Door 收到用戶端要求時，就會進行兩項作業。 如果您啟用快取，或將它們轉送至適當的應用程式後端作為反向 proxy，請回答這些問題。

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>選取適用於流量路由傳送的 Front Door 環境 (任一傳播)

路由傳送至 Azure Front Door 環境的流量會使用 DNS (網域名稱系統) 和 HTTP (超文字傳輸通訊協定) 流量的 [任意](https://en.wikipedia.org/wiki/Anycast) 傳播，如此可讓使用者要求達到最少網路躍點中最接近的環境。 此架構藉由充分發揮分割 TCP 的優點，為終端使用者提供更好的往返時間。 Front Door 會將其環境分類為主要及後援「圈」。 外圈具有較接近使用者的環境，並能提供較低的延遲。  內部環形具有可處理外部環形環境容錯移轉的環境，以防發生任何問題。 外部環形是所有流量慣用的目標，而內部環形則是處理來自外部環形的流量溢位。 Front Door 所服務的每個前端主機或網域都會被指派一個主要 VIP (虛擬網際網路通訊協定位址) ，而這會在內部和外部環形的環境中宣告。 只有內部通道中的環境會宣告 fallback VIP。 

此架構可確保來自您終端使用者的要求一律會到達最接近的 Front Door 環境。 即使慣用的 Front Door 環境狀況不良，所有流量都會自動移至下一個最接近的環境。

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>連線到 Front Door 環境 (分割 TCP)

[分割 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) \(英文\) 是一個可降低延遲與 TCP 問題的技術，能將會產生較高來回時間的連線分割成較小的片段。 當 Front Door 環境更接近終端使用者，TCP 連線會在 Front Door 環境內終止。 具有大量來回時間 (RTT) 應用程式後端的 TCP 連線，會分割成兩個不同的連接。 終端使用者與 Front Door 環境之間的「簡短連線」表示連線是透過三次短往返建立的，而不是三次來回行程，這會導致節省延遲的時間。 Front Door 環境與後端之間的「長連線」可以預先建立，然後在其他使用者要求節省連線時間。 當建立 SSL/TLS (傳輸層安全性) 連線時，分割 TCP 的效果會相乘，因為有更多的往返可保護連線的安全。

## <a name="processing-request-to-match-a-routing-rule"></a>處理要求以比對路由規則
建立連線並完成 TLS 信號交換之後，要求之後的第一個步驟就是在 Front Door 環境中，使其符合路由規則。 比對是由 Front Door 上的設定所決定，而這是要與要求相符的特定路由規則。 若要深入了解，請參閱 Front Door 執行[路由比對](front-door-route-matching.md)的方式。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>針對路由規則識別後端集區中的可用後端
一旦 Front Door 符合連入要求的路由規則，下一個步驟就是在沒有快取的情況下，取得與路由規則相關聯的後端集區的健康情況探查狀態。 若要深入了解，請參閱 Front Door 使用[健康情況探查](front-door-health-probes.md)來監視後端健康情況的方式。

## <a name="forwarding-the-request-to-your-application-backend"></a>將要求轉送到應用程式後端
最後，假設未設定快取，則會根據您的 [路由方法](front-door-routing-methods.md) 設定，將使用者要求轉送至「最佳」後端。

## <a name="next-steps"></a>後續步驟

- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
