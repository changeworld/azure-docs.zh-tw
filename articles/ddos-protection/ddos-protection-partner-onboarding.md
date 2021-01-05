---
title: 與 Azure DDoS 保護 Standard 合作
description: 瞭解 Azure DDoS 保護 Standard 所啟用的合作商機。
ms.service: ddos-protection
documentationcenter: na
author: yitoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 962f6bcf7747a82749e7ccab95de34f7834a8372
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814205"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>與 Azure DDoS 保護 Standard 合作
本文說明 Azure DDoS 保護標準所啟用的合作機會。 本文旨在協助產品經理和商務開發角色瞭解投資途徑，並提供合作價值主張的見解。

## <a name="background"></a>背景
分散式阻斷服務 (DDoS) 攻擊是客戶將應用程式移至雲端的最高可用性和安全性顧慮之一。 因為 extortion 和 hacktivism 是 DDoS 攻擊背後的常見動機，所以它們的類型、規模和發生頻率一直都是一致的，因為它們相當簡單且更便宜。

Azure DDoS 保護針對最複雜的 DDoS 威脅提供對策，利用 Azure 網路的全球規模。 此服務為部署在虛擬網路中的應用程式和資源，提供增強的 DDoS 風險降低功能。

技術合作夥伴可以使用 Azure DDoS 保護標準，以原生方式保護其客戶資源，以因應 DDoS 攻擊的可用性和可靠性問題。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 保護 Standard 簡介
Azure DDoS 保護 Standard 針對第3層和第4層 DDoS 攻擊提供增強的 DDoS 風險降低功能。 以下是 DDoS 保護標準服務的主要功能。

### <a name="adaptive-real-time-tuning"></a>調適性即時調整
針對每個受保護的應用程式，Azure DDoS 保護 Standard 會根據應用程式的流量配置檔案模式自動調整 DDoS 風險降低原則閾值。 服務會使用兩種見解來完成這項自訂：

- 自動學習每位客戶 (每個 IP) 的第 3 層與第 4 層流量模式。
- 考慮到 Azure 的規模會使之吸收大量的流量，進而降低誤判。

![適應性即時微調](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻擊分析、遙測、監視及警示
Azure DDoS 保護識別並減少 DDoS 攻擊，而不需要使用者介入。

- 如果受保護的資源位於 Azure 資訊安全中心所涵蓋的訂用帳戶中，則當偵測到 DDoS 攻擊並針對受保護的應用程式緩解時，DDoS 保護標準會自動將警示傳送至「安全性中心」。
- 或者，若要在受保護的公用 IP 有作用中的緩和措施時收到通知，您可以在 [DDoS 攻擊] 下設定計量的 [警示](alerts.md) 。
- 此外，您還可以選擇建立其他 DDoS 計量的警示，並 [設定攻擊遙測](telemetry.md) ，以瞭解攻擊的規模、要卸載的流量、攻擊媒介、熱門參與者和其他詳細資料。

![DDoS 計量](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速回應 (DRR) 
DDoS 保護標準客戶可在主動攻擊期間存取 [快速回應小組](ddos-rapid-response.md) 。 DRR 可協助在攻擊期間進行攻擊調查，以及進行攻擊後的分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保證和成本保護
DDoS 保護標準服務涵蓋99.99% 的 SLA，而「成本保護」會在記載的攻擊期間提供資源信用額度以進行 scale out。 如需詳細資訊，請參閱 [Azure DDoS 保護的 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)。

## <a name="featured-partner-scenarios"></a>精選合作夥伴案例
以下是您可以藉由整合 Azure DDoS 保護標準來衍生的主要優點：

- 合作夥伴提供的服務 (負載平衡器、web 應用程式防火牆、防火牆等，) 到其客戶的客戶會自動受到 (白名單中 Azure DDoS 保護標準所標示) 的保護。
- 合作夥伴可以存取 Azure DDoS 保護的標準攻擊分析和遙測，讓他們可以與自己的產品整合，以提供一致的客戶體驗。  
- 即使在沒有 Azure 快速回應的情況下，合作夥伴也可以存取 DDoS 快速回應支援，以瞭解 DDoS 的相關問題。
- 合作夥伴受保護的應用程式在發生 DDoS 攻擊時，會受到 DDoS SLA 保證和成本保護的支援。

## <a name="technical-integration-overview"></a>技術整合總覽
Azure DDoS 保護標準合作機會可透過 Azure 入口網站、Api 和 CLI/PS 來取得。

### <a name="integrate-with-ddos-protection-standard"></a>與 DDoS 保護標準整合
合作夥伴必須執行下列步驟，才能設定與 Azure DDoS 保護 Standard 的整合：
1. 在您想要的 (夥伴) 訂用帳戶中建立 DDoS 保護方案。 如需逐步指示，請參閱 [建立 DDoS 標準保護計劃](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 只需要為指定的租使用者建立1個 DDoS 保護方案。 
2. 在您的 (夥伴) 訂用帳戶中部署具有公用端點的服務，例如負載平衡器、防火牆及 web 應用程式防火牆。 
3. 使用第一個步驟中建立的 DDoS 保護計劃，在具有公用端點的服務的虛擬網路上啟用 Azure DDoS 保護標準。 如需 stpe 逐步指示，請參閱 [啟用 DDoS 標準保護計劃](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虛擬網路上啟用 Azure DDoS 保護 Standard 之後，該虛擬網路內的所有公用 Ip 都會自動受到保護。 這些公用 Ip 的來源可以是 Azure (用戶端訂用帳戶) 或 Azure 外部。 
4. （選擇性）將 Azure DDoS 保護標準遙測和攻擊分析整合至應用程式特定的客戶面向儀表板。 如需使用遙測的詳細資訊，請參閱 [查看和設定 DDoS 保護遙測](telemetry.md)。 

### <a name="onboarding-guides-and-technical-documentation"></a>上架指南和技術檔

- [Azure DDoS 保護產品頁面](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 保護文件](ddos-protection-overview.md)
- [Azure DDoS 保護 API 參考](/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虛擬網路 API 參考](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>取得說明

- 如果您有關于使用 Azure DDoS 保護 Standard 的應用程式、服務或產品整合的問題，請與 [Azure 安全性社區](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)聯繫。
- 遵循 [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)的討論。

### <a name="get-to-market"></a>進入市場

- 與 Microsoft 合作的主要程式是 [Microsoft 合作夥伴網路](https://partner.microsoft.com/)。 – Microsoft Graph 的安全性整合融入 [MPN 獨立軟體廠商 (ISV) ](https://partner.microsoft.com/saas-solution-guide) 的追蹤。
- [Microsoft 智慧型安全性關聯](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) 是專為 Microsoft 安全性合作夥伴提供的計畫，可協助豐富您的安全性產品，並改善您與 Microsoft 安全性產品整合的客戶探索能力。

## <a name="next-steps"></a>後續步驟
查看現有的合作夥伴整合：

- [Barracuda WAF 即服務](https://www.barracuda.com/waf-as-a-service)
- [來自 Radware 的 Azure Cloud WAF](https://www.radware.com/resources/microsoft-azure/)