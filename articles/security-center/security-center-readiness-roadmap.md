---
title: Azure 資訊安全中心整備藍圖 | Microsoft Docs
description: 本文件提供進一步了解 Azure 資訊安全中心的整備藍圖。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 38e1fc7f062908f8c4a4b9aa903ca09dd7fb9ea5
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342326"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure 資訊安全中心就緒藍圖
本文件提供的整備藍圖將協助您開始使用 Azure 資訊安全中心。

## <a name="understanding-security-center"></a>了解資訊安全中心
Azure 資訊安全中心提供統一的安全性管理和進階的威脅防護，保護 Azure、內部部署和其他雲端中執行的工作負載。 

使用下列資源來開始使用資訊安全中心。

發行項
- [Azure 資訊安全中心簡介](security-center-introduction.md)
- [Azure 資訊安全中心快速入門指南](security-center-get-started.md)

影片
- [快速簡介影片](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [資訊安全中心的防護、偵測及回應功能概觀](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>規劃與作業

若要充分利用資訊安全中心，務必了解您組織中的不同人員或小組如何使用此服務，來滿足安全的作業、監視、控管和事件回應的需求。

使用下列資源可協助您進行規劃和操作程序。

- [Azure 資訊安全中心規劃和操作指南](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>將電腦上架到資訊安全中心
「安全性中心」會自動偵測任何未受 Azure Defender 保護的 Azure 訂用帳戶或工作區。 這包括使用未啟用安全性解決方案的安全中心免費和工作區的 Azure 訂用帳戶。

使用下列資源可協助您進行上架程序。

- [上架非 Azure 電腦](quickstart-onboard-machines.md)
- [Azure 資訊安全中心混合式資源 - 概觀](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>使用資訊安全中心緩和安全性問題
資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。

使用下列資源可協助您管理安全性警示及保護您的資源。

發行項    
- [Azure 資訊安全中心的安全性健康情況監視](./security-center-monitoring.md)
- [保護 Azure 資訊安全中心內的網路](./security-center-network-recommendations.md)
- [保護 Azure 資訊安全中心內的 Azure SQL 服務和資料](./security-center-remediate-recommendations.md)


影片    
- [使用 Azure 資訊安全中心緩和安全性問題](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>可供進行事件回應的資訊安全中心
為了降低成本和損害，一定要在攻擊發生前備妥事件回應計劃。 您可以在不同階段的事件回應使用 Azure 資訊安全中心。

使用下列資源可了解資訊安全中心如何併入您的事件回應程序中。

影片    
* [用於事件回應的 Azure 資訊安全中心](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [透過新一代安全性作業和調查來快速回應威脅](https://youtu.be/e8iFCz5RM4g)

發行項    
* [使用 Azure 資訊安全中心進行事件回應](./tutorial-security-incident.md)
* [使用自動化工作流程來自動回應](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>進階雲端防禦

Azure VM 可以利用資訊安全中心的進階雲端防禦功能。 這些功能包括 Just-In-Time 虛擬機器 (VM) 存取及自適性應用程式控制。

使用下列資源來了解如何在資訊安全中心使用這些功能。

影片    
* [Azure 資訊安全中心 - Just-in-Time VM 存取](https://youtu.be/UOQb2FcdQnU)
* [Azure 資訊安全中心 - 自適性應用程式控制](https://youtu.be/wWWekI1Y9ck)

發行項    
* [使用 Just-In-Time 管理虛擬機器存取](./security-center-just-in-time.md)
* [Azure 資訊安全中心的自適性應用程式控制](./security-center-adaptive-application.md)

## <a name="hands-on-activities"></a>實際操作活動

* [資訊安全中心實際操作實驗室](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [資訊安全中心的 Web 應用程式防火牆 (WAF) 建議腳本](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure 資訊安全中心劇本：安全性警示](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>其他資源
* [資訊安全中心文件頁面](./index.yml)
* [資訊安全中心 REST API 文件頁面](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Azure 資訊安全中心常見問題集 (FAQ)](./faq-general.md)
* [資訊安全中心價格頁面](https://azure.microsoft.com/pricing/details/security-center/)
* [身分識別安全性最佳做法](../security/fundamentals/identity-management-best-practices.md)
* [網路安全性的最佳做法](../security/fundamentals/network-best-practices.md)
* [PaaS 建議](../security/fundamentals/paas-deployments.md)
* [遵循](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [記錄分析客戶現在可以使用 Azure 資訊安全中心保護其混合式雲端工作負載](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)

## <a name="community-resources"></a>社群資源

* [資訊安全中心 UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [問&安全性中心的頁面](/answers/topics/azure-security-center.html)