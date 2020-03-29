---
title: 與 Azure DDoS 保護標準合作
description: 瞭解 Azure DDoS 保護標準啟用的合作夥伴商機。
ms.service: virtual-network
documentationcenter: na
author: anupamvi
mms.devlang: na
ms.topic: article
ms.date: 01/28/2020
ms.author: kumud
ms.openlocfilehash: 39cb2f90b49725c78746df69505fad7160e1db92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849678"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>與 Azure DDoS 保護標準合作
本文介紹 Azure DDoS 保護標準啟用的合作夥伴機會。 本文旨在説明產品經理和業務開發角色瞭解投資路徑，並提供對合作夥伴價值主張的洞察。

## <a name="background"></a>背景
分散式阻斷服務 （DDoS） 攻擊是客戶將其應用程式遷移到雲時表達的首要可用性和安全問題之一。 由於敲詐勒索和駭客主義是 DDoS 攻擊的常見動機，因此在類型、規模和發生頻率方面一直在不斷增加，因為它們相對容易且成本較低。

Azure DDoS 保護利用 Azure 網路的全球規模，針對最複雜的 DDoS 威脅提供對策。 該服務為部署在虛擬網路中的應用程式和資源提供了增強的 DDoS 緩解功能。

技術合作夥伴可以使用 Azure DDoS 保護標準從本地保護客戶的資源，以解決由於 DDoS 攻擊而導致的可用性和可靠性問題。

## <a name="introduction-to-azure-ddos-protection-standard"></a>Azure DDoS 保護標準簡介
Azure DDoS 保護標準針對第 3 層和第 4 層 DDoS 攻擊提供了增強的 DDoS 緩解功能。 以下是 DDoS 保護標準服務的主要功能。

### <a name="adaptive-real-time-tuning"></a>調適性即時調整
對於每個受保護的應用程式，Azure DDoS 保護標準會根據應用程式的流量配置檔案模式自動調整 DDoS 緩解策略閾值。 服務會使用兩種見解來完成這項自訂：

- 自動學習每位客戶 (每個 IP) 的第 3 層與第 4 層流量模式。
- 考慮到 Azure 的規模會使之吸收大量的流量，進而降低誤判。

![自我調整即時調諧](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>攻擊分析、遙測、監視和警報
Azure DDoS 保護無需任何使用者干預即可識別和緩解 DDoS 攻擊。

- 如果受保護的資源位於 Azure 安全中心涵蓋的訂閱中，則每當檢測到 DDoS 攻擊並針對受保護的應用程式緩解時，DDoS 保護標準會自動向安全中心發送警報。
- 或者，要在受保護公共 IP 有活動緩解時收到通知，可以在 DDoS 攻擊下的指標上[配置警報](manage-ddos-protection.md#configure-alerts-for-ddos-protection-metrics)。
- 此外，您還可以選擇為其他 DDoS 指標創建警報，並[配置攻擊分析](manage-ddos-protection.md#configure-ddos-attack-analytics)以瞭解攻擊的規模、丟棄的流量、攻擊媒介、頂級貢獻者和其他詳細資訊。

![DDoS 指標](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS 快速回應 （DRR）
DDoS 保護標準客戶在主動攻擊期間可以訪問[快速回應團隊](https://azure.microsoft.com/blog/ddos-protection-attack-analytics-rapid-response/)。 DRR 可説明進行攻擊調查、攻擊期間的自訂緩解以及攻擊後分析。

### <a name="sla-guarantee-and-cost-protection"></a>SLA 保證和成本保護
DDoS 保護標準服務由 99.99% SLA 覆蓋，成本保護為在有文檔記錄的攻擊期間橫向擴展提供了資源積分。 有關詳細資訊，請參閱[Azure DDoS 保護 的 SLA。](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/)

## <a name="featured-partner-scenarios"></a>精選合作夥伴方案
以下是通過與 Azure DDoS 保護標準集成可以獲得的主要優勢：

- 合作夥伴提供給其客戶的服務（負載等化器、Web 應用程式防火牆、防火牆等）由後端的 Azure DDoS 保護標準自動保護（白色標記）。
- 合作夥伴可以訪問 Azure DDoS 保護標準攻擊分析和遙測，他們可以與自己的產品集成，提供統一的客戶體驗。  
- 合作夥伴可以訪問 DDoS 快速回應支援，即使在 Azure 沒有快速回應的情況下，也可以訪問與 DDoS 相關的問題。
- 合作夥伴的受保護應用程式在 DDoS 攻擊時由 DDoS SLA 保證和成本保護提供支援。

## <a name="technical-integration-overview"></a>技術集成概述
Azure DDoS 保護標準合作夥伴機會可通過 Azure 門戶、API 和 CLI/PS 提供。

### <a name="integrate-with-ddos-protection-standard"></a>與 DDoS 保護標準集成
合作夥伴需要以下步驟來配置與 Azure DDoS 保護標準的集成：
1. 在所需的（合作夥伴）訂閱中創建 DDoS 保護計劃。 有關分步說明，請參閱[創建 DDoS 標準保護計劃](manage-ddos-protection.md#create-a-ddos-protection-plan)。
   > [!NOTE]
   > 只需為給定租戶創建 1 個 DDoS 保護計劃。 
2. 在（合作夥伴）訂閱中部署具有公共終結點的服務，例如負載等化器、防火牆和 Web 應用程式防火牆。 
3. 使用第一步中創建的 DDoS 保護計劃，在具有公共終結點的服務的虛擬網路上啟用 Azure DDoS 保護標準。 有關分步說明，請參閱[啟用 DDoS 標準保護計劃](manage-ddos-protection.md#enable-ddos-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > 在虛擬網路上啟用 Azure DDoS 保護標準後，該虛擬網路中的所有公共 IP 將自動受到保護。 這些公共 IP 的源可以是 Azure（用戶端訂閱）內，也可以是 Azure 之外。 
4. 或者，將 Azure DDoS 保護標準遙測和攻擊分析集成到特定于應用程式的面向客戶的儀表板中。 有關使用遙測的詳細資訊，請參閱[使用 DDoS 保護遙測](manage-ddos-protection.md#use-ddos-protection-telemetry)。 有關配置攻擊分析的詳細資訊，請參閱[配置 DDoS 攻擊分析](manage-ddos-protection.md#configure-ddos-attack-analytics)

### <a name="onboarding-guides-and-technical-documentation"></a>入職指南和技術文檔

- [Azure DDoS 保護產品頁面](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS 保護文件](ddos-protection-overview.md)
- [Azure DDoS 保護 API 引用](https://docs.microsoft.com/rest/api/virtualnetwork/ddosprotectionplans)
- [Azure 虛擬網路 API 引用](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>取得說明

- 如果對使用 Azure DDoS 保護標準的應用程式、服務或產品集成有疑問，請聯繫 Azure[安全社區](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security)。
- 關注關於[堆疊溢位](https://stackoverflow.com/tags/azure-ddos/)的討論。

### <a name="get-to-market"></a>進入市場

- 與微軟合作的主要計畫是[微軟合作夥伴網路](https://partner.microsoft.com/)。 • Microsoft 圖形安全集成屬於[MPN 獨立軟體廠商 （ISV）](https://partner.microsoft.com/saas-solution-guide)軌道。
- [Microsoft 智慧安全協會](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1)是專門針對 Microsoft 安全合作夥伴的計畫，旨在説明您豐富安全產品並提高客戶對 Microsoft 安全產品集成的可發現性。

## <a name="next-steps"></a>後續步驟
查看現有合作夥伴集成：

- [梭子魚 WAF 服務](https://www.barracuda.com/waf-as-a-service)
- [來自 Radware 的 Azure 雲 WAF](https://www.radware.com/resources/microsoft-azure/)
