---
title: DDoS 回應策略的元件
description: 瞭解如何使用 Azure DDoS 保護標準來回應 DDoS 攻擊。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814052"
---
# <a name="components-of-a-ddos-response-strategy"></a>DDoS 回應策略的元件

從使用者的觀點來看，以 Azure 資源為目標的 DDoS 攻擊所需的介入最少。 儘管如此，仍要將 DDoS 風險降低納入為事件回應策略的一部分，如此有助於盡可能降低對營運能否持續運作的影響。

## <a name="microsoft-threat-intelligence"></a>Microsoft 威脅情報

Microsoft 擁有廣大的威脅情報網路。 此網路會使用延伸安全性社群的共同知識，來支援 Microsoft 線上服務、Microsoft 合作夥伴，以及網際網路安全性社群內的關聯性。 

Microsoft 身為重要的基礎結構提供者，會最先收到威脅的警告。 Microsoft 會從自己的線上服務及全球的客戶群收集威脅情報。 Microsoft 會將所有的威脅情報整併回其 Azure DDoS 保護產品中。

此外，Microsoft 數位犯罪防治中心 (DCU) 會對殭屍網路採取進攻策略。 殭屍網路是一種常見的 DDoS 攻擊指揮與控制來源。

## <a name="risk-evaluation-of-your-azure-resources"></a>Azure 資源的風險評估

請務必持續不斷地了解遭受 DDoS 攻擊的風險範圍。 請定期自問：

- 有哪些公開使用的 Azure 新資源需要保護？

- 服務中有單一失敗點嗎？ 

- 如何將服務隔離以限制攻擊所產生的影響，同時仍能讓有效的客戶可以使用服務？

- 是否有虛擬網路應啟用標準 DDoS 保護，但尚未啟用？ 

- 我的服務是否會在跨多個區域進行容錯移轉時處於主動/主動狀態？

您必須瞭解應用程式的正常行為，並準備好讓應用程式在 DDoS 攻擊期間未如預期般運作，是很重要的。 為您的商務關鍵應用程式設定監視，以模擬用戶端行為，並在偵測到相關的異常時通知您。 若要深入瞭解您應用程式的健康情況，請參閱 [監視和診斷最佳做法](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) 。

[Azure Application Insights](../azure-monitor/app/app-insights-overview.md) 是多個平台上的 Web 開發人員所適用的可延伸應用程式效能管理 (APM) 服務。 使用 Application Insights 監視您的即時 Web 應用程式。 它會自動偵測效能異常。 它包含分析工具，可協助您診斷問題，並瞭解使用者如何運用您的應用程式。 它是設計來協助您持續改善效能和可用性。

## <a name="customer-ddos-response-team"></a>客戶 DDoS 回應小組

成立 DDoS 回應小組是快速有效回應攻擊的關鍵步驟。 請找出組織中應負責監督規劃與執行的連絡人。 此 DDoS 回應小組應徹底了解 Azure 標準 DDoS 保護服務。 請確保小組能與內部和外部客戶 (包括 Microsoft 支援小組) 同心協力找出攻擊並降低風險。 

建議您在服務可用性和持續性規劃中使用模擬練習，這些練習應該包含調整測試。 請參閱 [透過模擬進行測試](test-through-simulations.md) ，以瞭解如何模擬 Azure 公用端點的 DDoS 測試流量。

## <a name="alerts-during-an-attack"></a>攻擊時的警示

Azure 標準 DDoS 保護會識別 DDoS 攻擊並降低風險，無須使用者介入。 若要在受保護的公用 IP 有作用中的緩和措施時收到通知，您可以 [設定警示](alerts.md)。

### <a name="when-to-contact-microsoft-support"></a>連絡 Microsoft 支援服務的時機

Azure DDoS 保護標準客戶可以存取 DDoS 快速回應 (DRR) 小組，以協助在攻擊期間進行攻擊調查，以及進行攻擊後的分析。 如需詳細資訊，請參閱 [DDoS 快速回應](ddos-rapid-response.md) ，包括何時應參與 DRR 團隊。

## <a name="post-attack-steps"></a>攻擊發生後的步驟

遭受攻擊之後進行事後檢討，並視需要重新調整 DDoS 回應策略，永遠都是個不錯的策略。 需考量的事項：

- 是否因為缺少可調整規模的架構而使得服務或使用者體驗發生任何中斷？

- 哪些應用程式或服務發生最多？

- DDoS 回應策略的有效性如何，以及如何加以改善？

如果您懷疑遭到 DDoS 攻擊，請透過一般的 Azure 支援管道呈報。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [建立 DDoS 保護計劃](manage-ddos-protection.md)。