---
title: 在 Microsoft Azure 上部署安全的應用程式
description: 本文討論 web 應用程式專案的發行和回應階段期間所要考慮的最佳做法。
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/12/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: dfe4f09d00a5629249a3041946190f56e83c3480
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "68934877"
---
# <a name="deploy-secure-applications-on-azure"></a>在 Azure 上部署安全的應用程式
在本文中，我們會提供部署雲端應用程式時應考慮的安全性活動和控制項。 在 Microsoft[安全性開發週期（SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的發行和回應階段，涵蓋了要考慮的安全性問題和概念。 其目標是要協助您定義活動和 Azure 服務，您可以用來部署更安全的應用程式。

本文涵蓋下列 SDL 階段：

- 版本
- 回應

## <a name="release"></a>版本
發行階段的重點是讓專案公開發行。
這包括可有效執行發行後服務工作，以及解決稍後可能發生之安全性弱點的規劃方式。

### <a name="check-your-applications-performance-before-you-launch"></a>啟動之前，請檢查您的應用程式效能

在啟動應用程式或將更新部署至生產環境之前，請先檢查其效能。 使用 Visual Studio 來執行雲端式[負載測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)，以找出應用程式中的效能問題、改善部署品質、確定您的應用程式一律啟動或可用，以及您的應用程式可以處理您的啟動流量。

### <a name="install-a-web-application-firewall"></a>安裝 Web 應用程式防火牆

Web 應用程式已逐漸成為利用常見已知弱點的惡意攻擊目標。 在這些攻擊中，常見的是 SQL 插入式攻擊和跨網站腳本攻擊。 防止這些攻擊在應用程式代碼中變得很困難。 在應用程式拓撲的許多層級，它可能需要嚴格的維護、修補和監視。 集中式 WAF 有助於簡化安全性管理。 WAF 解決方案也可以透過在中央位置修補已知弱點，而不是保護每個個別的 web 應用程式，來回應安全性威脅。

[Azure 應用程式閘道 WAF](../../application-gateway/waf-overview.md)提供 web 應用程式的集中式保護，免于遭受常見的攻擊和弱點。 WAF 是以[OWASP 核心規則集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或2.2.9 的規則為基礎。

### <a name="create-an-incident-response-plan"></a>建立事件回應計劃

準備事件回應計畫非常重要，可協助您解決可能隨著時間而出現的新威脅。 準備事件回應計畫包括識別適當的安全性緊急連絡人，並針對繼承自組織中其他群組和授權協力廠商程式碼的程式碼，建立安全性服務方案。

### <a name="conduct-a-final-security-review"></a>進行最後的安全性審查

刻意檢查所有執行的安全性活動，有助於確保軟體版本或應用程式的就緒。 最後的安全性審查（FSR）通常包括檢查威脅模型、工具輸出，以及在需求階段中定義的品質管制和錯誤列的效能。

### <a name="certify-release-and-archive"></a>認證發行與封存

在發行之前認證軟體有助於確保符合安全性和隱私權需求。 封存所有相關資料對於執行發行後服務工作而言是不可或缺的。 封存也有助於降低與持續軟體工程相關聯的長期成本。

## <a name="response"></a>回應
開發小組的回應發行後階段中心，可以適當地回應任何新興軟體威脅和弱點的報告。

### <a name="execute-the-incident-response-plan"></a>執行事件回應計畫

為了協助保護客戶免于出現的軟體安全性或隱私權弱點，必須能夠實現在發行階段中所建立的事件回應計畫。

### <a name="monitor-application-performance"></a>監視應用程式的效能

您的應用程式在部署後持續進行監視，可能會協助您偵測效能問題以及安全性弱點。
協助應用程式監視的 Azure 服務包括：

  - Azure Application Insights
  - Azure 資訊安全中心

#### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md)是可延伸的應用程式效能管理（APM）服務，適用于多個平臺上的 網頁程式開發人員。 您可以使用它來監視即時 Web 應用程式。 Application Insights 會自動偵測效能異常。 其中包含功能強大的分析工具，可協助您診斷問題，並瞭解使用者實際如何使用您的應用程式。 它是設計來協助您持續改善效能和可用性。

#### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 資訊安全中心](../../security-center/security-center-intro.md)可協助您防止、偵測及回應威脅，並加強對 Azure 資源安全性的可見度（及控制），包括 web 應用程式。 Azure 資訊安全中心有助於偵測可能不會察覺到的威脅。 其適用于各種安全性解決方案。

資訊安全中心的免費層僅為 Azure 資源提供有限的安全性。 [資訊安全中心標準層](../../security-center/security-center-onboarding.md)將這些功能延伸到內部部署資源和其他雲端。
資訊安全中心 Standard 可協助您：

  - 尋找並修正安全性弱點。
  - 套用存取和應用程式控制以封鎖惡意活動。
  - 流量分析和情報來偵測威脅。
  - 在遭受攻擊時迅速回應。

## <a name="next-steps"></a>後續步驟
在下列文章中，我們建議可協助您設計和開發安全應用程式的安全性控制和活動。

- [設計安全的應用程式](secure-design.md)
- [開發安全的應用程式](secure-develop.md)
