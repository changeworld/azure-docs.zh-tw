---
title: Azure Front Door-DDoS 保護
description: 此頁面提供 Azure Front Door 如何協助防範 DDoS 攻擊的相關資訊
services: frontdoor
documentationcenter: ''
author: johndowns
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: jodowns
ms.openlocfilehash: 24a8a559d72eae4dab0b6e740b34890f1d7ff114
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350778"
---
# <a name="ddos-protection-on-front-door"></a>Front Door 上的 DDoS 保護

Azure Front Door 有幾個功能和特性，可協助防止分散式阻斷服務 (DDoS) 的攻擊。 這些功能可以防止攻擊者抵達您的應用程式，並影響應用程式的可用性和效能。

## <a name="integration-with-azure-ddos-protection-basic"></a>與 Azure DDoS 保護 Basic 整合

Front Door 是由 Azure DDoS 保護 Basic 保護。 依預設，它會整合到 Front Door 平臺，而且沒有額外的費用。 Front Door 全球部署網路的完整規模和容量，可透過永遠可用的流量監視和即時風險降低，來防禦常見的網路層攻擊。 基本的 DDoS 保護也可以抵禦最常見、經常發生的第7層 DNS 查詢氾濫，以及以公用端點為目標的第3層和第4體積型攻擊。 這項服務也有經過證實的追蹤記錄，可保護 Microsoft 的企業和取用者服務免于大規模的攻擊。 如需詳細資訊，請參閱 [Azure DDoS 保護](../security/fundamentals/ddos-best-practices.md)。

## <a name="protocol-blocking"></a>封鎖的通訊協定

Front Door 只接受 HTTP 和 HTTPS 通訊協定上的流量，而且只會處理具有已知標頭的有效要求 `Host` 。 此行為有助於減輕一些常見的 DDoS 攻擊類型，包括散佈在一系列通訊協定和埠、DNS 放大攻擊和 TCP 中毒攻擊的體積型攻擊。

## <a name="capacity-absorption"></a>容量吸收

Front Door 是大規模規模的全球分散式服務。 我們有許多客戶，包括 Microsoft 本身的大規模雲端產品，每秒都會收到數十萬個要求。 Front Door 位於 Azure 網路的邊緣，吸收和地理位置隔離大量的攻擊。 這可防止惡意流量超出 Azure 網路的邊緣。

## <a name="caching"></a>快取

[Front Door 的](./front-door-caching.md) 快取功能可以用來保護後端，使其免于遭受攻擊所產生的大型流量。 快取的資源會從 Front Door 邊緣節點傳回，因此不會轉送到您的後端。 快取的快取到期時間 (秒或分鐘) 動態回應可能會大幅減少後端服務的負載。 如需快取概念和模式的詳細資訊，請參閱快取 [考慮](/azure/architecture/best-practices/caching) 和另行快取 [模式](/azure/architecture/patterns/cache-aside)。

## <a name="web-application-firewall-waf"></a>Web 應用程式防火牆 (WAF)

[Front Door 的 Web 應用程式防火牆 (WAF) ](../web-application-firewall/afds/afds-overview.md) 可以用來減輕一些不同類型的攻擊：

* 使用受控規則集可針對一些常見的攻擊提供保護。
* 從定義的地理區域外部或在定義區域內的流量，可以被封鎖或重新導向至靜態網頁。 如需詳細資訊，請參閱 [地理篩選](../web-application-firewall/afds/waf-front-door-geo-filtering.md)。
* 您識別為惡意的 IP 位址和範圍可能會遭到封鎖。
* 您可以套用速率限制，以防止 IP 位址太頻繁地呼叫您的服務。
* 您可以建立 [自訂 WAF 規則](../web-application-firewall/afds/waf-front-door-custom-rules.md) ，以自動封鎖有已知簽章的 HTTP 或 HTTPS 攻擊，並對其進行速率限制。

## <a name="for-further-protection"></a>進一步保護

如果您需要進一步保護，您可以在您的後端部署所在的 VNet 上啟用 [Azure DDoS 保護 Standard](../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) 。 DDoS 保護標準客戶享有額外的權益，包括成本保護、SLA 保證，以及來自 DDoS 快速回應小組的專家存取，以在攻擊期間提供立即協助。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [在 Front Door 上設定 WAF 設定檔](front-door-waf.md)。 
- 了解如何[建立 Front Door](quickstart-create-front-door.md)。
- 了解 [Front Door 的運作方式](front-door-routing-architecture.md)。