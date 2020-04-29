---
title: 與 Azure DDoS 保護 Standard 合作
description: 瞭解 Azure DDoS 保護標準啟用的合作機會。
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849678"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>與 Azure DDoS 保護 Standard 合作
本文說明 Azure DDoS 保護標準所啟用的合作機會。 本文旨在協助產品經理和商務開發角色瞭解投資途徑，並讓您深入瞭解合作價值主張。

## <a name="background"></a>背景
分散式阻斷服務（DDoS）攻擊是客戶將應用程式移至雲端的最高可用性和安全性顧慮之一。 由於 extortion 和 hacktivism 是 DDoS 攻擊的常見動機，因此它們的類型、規模和發生頻率一直不斷增加，因為它們相當簡單且更便宜。

Azure DDoS 保護針對最複雜的 DDoS 威脅提供對策，利用 Azure 網路的全球規模。 此服務可針對部署在虛擬網路中的應用程式和資源，提供增強的 DDoS 風險降低功能。

技術合作夥伴可以使用 Azure DDoS 保護標準，以原生方式保護其客戶的資源，以因應 DDoS 攻擊的可用性和可靠性問題。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 保護 Standard 簡介
Azure DDoS 保護 Standard 針對第3層和第4層 DDoS 攻擊提供增強的 DDoS 風險降低功能。 以下是 DDoS 保護標準服務的主要功能。

### <a name="adaptive-real-time-tuning"></a>調適性即時調整
針對每個受保護的應用程式，Azure DDoS 保護 Standard 會根據應用程式的流量配置檔案模式自動調整 DDoS 風險降低原則閾值。 服務會使用兩種見解來完成這項自訂：

- 自動學習每位客戶 (每個 IP) 的第 3 層與第 4 層流量模式。
- 考慮到 Azure 的規模會使之吸收大量的流量，進而降低誤判。

![彈性即時微調](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻擊分析、遙測、監視和警示
Azure DDoS 保護識別並減少 DDoS 攻擊，而不需要使用者介入。

- 如果受保護的資源是在 Azure 資訊安全中心所涵蓋的訂用帳戶中，DDoS 保護標準會在偵測到 DDoS 攻擊時，自動傳送警示給資訊安全中心，並針對受保護的應用程式降低。
- 或者，若要在受保護的公用 IP 有作用中的緩和措施時收到通知，您可以在 [DDoS 攻擊] 底下的 [度量] 上[設定警示](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)。
- 您還可以選擇為其他 DDoS 計量建立警示，並[設定攻擊分析](manage-ddos-protection.md#configure-ddos-attack-analytics)來瞭解攻擊的規模、丟棄的流量、攻擊媒介、熱門參與者，以及其他詳細資料。

![DDoS 計量](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速回應（DRR）
DDoS 保護標準客戶可以在主動攻擊期間存取[快速回應小組](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/)。 DRR 可協助攻擊調查、攻擊期間的自訂緩和措施，以及攻擊後的分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保證和成本保護
「DDoS 保護標準」服務涵蓋99.99% 的 SLA，而「成本保護」會在記載的攻擊期間，為相應放大提供資源信用額度。 如需詳細資訊，請參閱[Azure DDoS 保護的 SLA](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)。

## <a name="featured-partner-scenarios"></a>精選合作夥伴案例
以下是您可以藉由與 Azure DDoS 保護標準整合來衍生的主要優點：

- 合作夥伴提供給其客戶的服務（負載平衡器、web 應用程式防火牆、防火牆等）會在後端 Azure DDoS 保護標準自動受到保護（加上標籤）。
- 合作夥伴可以存取 Azure DDoS 保護標準攻擊分析和遙測資料，以與他們自己的產品整合，提供統一的客戶體驗。  
- 即使沒有 Azure 快速回應，合作夥伴也可以存取 DDoS 快速回應支援，以取得 DDoS 相關問題。
- 合作夥伴受保護的應用程式在發生 DDoS 攻擊時，會受到 DDoS SLA 保證和成本保護的支援。

## <a name="technical-integration-overview"></a>技術整合總覽
Azure DDoS 保護標準的合作機會可透過 Azure 入口網站、Api 和 CLI/PS 取得。

### <a name="integrate-with-ddos-protection-standard"></a>與 DDoS 保護標準整合
若要設定與 Azure DDoS 保護 Standard 的整合，合作夥伴需要下列步驟：
1. 在您想要的（夥伴）訂用帳戶中建立 DDoS 保護計劃。 如需逐步指示，請參閱[建立 DDoS 標準保護計劃](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 針對指定的租使用者，只需要建立1個 DDoS 保護計劃。 
2. 在您（合作夥伴）的訂用帳戶中部署具有公用端點的服務，例如負載平衡器、防火牆和 web 應用程式防火牆。 
3. 使用第一個步驟中建立的 DDoS 保護計劃，在具有公用端點之服務的虛擬網路上啟用 Azure DDoS 保護標準。 如需 stpe 逐步指示，請參閱[啟用 DDoS 標準保護計劃](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虛擬網路上啟用 Azure DDoS 保護 Standard 之後，該虛擬網路內的所有公用 Ip 都會自動受到保護。 這些公用 Ip 的來源可以是 Azure （用戶端訂用帳戶）或 Azure 外部。 
4. （選擇性）在應用程式特定的客戶面向儀表板中整合 Azure DDoS 保護標準遙測和攻擊分析。 如需使用遙測的詳細資訊，請參閱[使用 DDoS 保護遙測](manage-ddos-protection.md#use-ddos-protection-telemetry)。 如需設定攻擊分析的詳細資訊，請參閱[設定 DDoS 攻擊分析](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>上線指南和技術檔

- [Azure DDoS 保護產品頁面](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 保護文件](ddos-protection-overview.md)
- [Azure DDoS 保護 API 參考](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虛擬網路 API 參考](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>取得說明

- 如果您對於使用 Azure DDoS 保護 Standard 的應用程式、服務或產品整合有任何疑問，請與[Azure 安全性社區](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)聯繫。
- 請遵循[Stack Overflow](https://stackoverflow.com/tags/azure-ddos/)的討論。

### <a name="get-to-market"></a>進入市場

- 與 Microsoft 合作的主要程式是[Microsoft 合作夥伴網路](https://partner.microsoft.com/)。 – Microsoft Graph 安全性整合屬於[MPN 獨立軟體廠商（ISV）](https://partner.microsoft.com/saas-solution-guide)的追蹤。
- [Microsoft 智慧型安全性協會](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1)是特別為 Microsoft 安全性合作夥伴提供的計畫，可協助您擴充安全性產品，並改善客戶對 microsoft 安全性產品的整合性。

## <a name="next-steps"></a>後續步驟
觀看現有的合作夥伴整合：

- [Barracuda WAF 即服務](https://www.barracuda.com/waf-as-a-service)
- [來自 Radware 的 Azure Cloud WAF](https://www.radware.com/resources/microsoft-azure/)
