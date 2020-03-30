---
title: 在 Microsoft Azure 上部署安全應用程式
description: 本文討論了在 Web 應用程式專案的發佈和回應階段需要考慮的最佳做法。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "68934877"
---
# <a name="deploy-secure-applications-on-azure"></a>在 Azure 上部署安全應用程式
在本文中，我們將介紹在為雲部署應用程式時要考慮的安全活動和控制措施。 介紹了在 Microsoft[安全開發生命週期 （SDL）](https://msdn.microsoft.com/library/windows/desktop/84aed186-1d75-4366-8e61-8d258746bopq.aspx)的發佈和回應階段需要考慮的安全問題和概念。 目標是説明您定義可用於部署更安全應用程式的活動和 Azure 服務。

本文將介紹以下 SDL 階段：

- 版本
- 回應

## <a name="release"></a>版本
發佈階段的重點是為公開發布的專案做好準備。
這包括規劃有效執行發佈後服務任務的方法，並解決以後可能出現的安全性漏洞。

### <a name="check-your-applications-performance-before-you-launch"></a>啟動前檢查應用程式的性能

在啟動應用程式或將更新部署到生產中之前，請檢查應用程式的性能。 通過使用 Visual Studio 來查找應用程式中的性能問題、提高部署品質、確保應用程式始終處於打開或可用，以及應用程式可以處理啟動流量，從而運行基於雲的[負載測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)。

### <a name="install-a-web-application-firewall"></a>安裝 Web 應用程式防火牆

Web 應用程式已逐漸成為利用常見已知弱點的惡意攻擊目標。 這些漏洞中常見的是 SQL 注入攻擊和跨網站腳本攻擊。 在應用程式代碼中防止這些攻擊可能具有挑戰性。 它可能需要在應用程式拓撲的許多層進行嚴格的維護、修補和監視。 集中式 WAF 有助於簡化安全管理。 WAF 解決方案還可以通過在中心位置修補已知漏洞而不是保護每個單獨的 Web 應用程式來回應安全威脅。

[Azure 應用程式閘道 WAF](../../application-gateway/waf-overview.md)提供 Web 應用程式的集中保護，防止常見漏洞和漏洞。 WAF 基於[OWASP 核心規則集](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)3.0 或 2.2.9 中的規則。

### <a name="create-an-incident-response-plan"></a>建立事件回應計劃

準備事件回應計畫對於説明您解決隨時間而出現的新威脅至關重要。 準備事件回應計畫包括確定適當的安全緊急連絡人，並為從組織中的其他組繼承的代碼和許可的協力廠商代碼建立安全服務方案。

### <a name="conduct-a-final-security-review"></a>進行最終安全審查

故意檢查執行的所有安全活動有助於確保軟體發佈或應用程式準備就緒。 最終安全審查 （FSR） 通常包括針對需求階段定義的品質門和 Bug 欄檢查威脅模型、工具輸出和性能。

### <a name="certify-release-and-archive"></a>認證發佈和存檔

在發佈之前對軟體進行認證有助於確保滿足安全和隱私要求。 存檔所有相關資料對於執行發佈後服務任務至關重要。 歸檔還有助於降低與持續軟體工程相關的長期成本。

## <a name="response"></a>回應
發佈後回應階段的核心是開發團隊能夠而且能夠對任何新出現的軟體威脅和漏洞的報告做出適當的回應。

### <a name="execute-the-incident-response-plan"></a>執行事件回應計畫

能夠實施發佈階段啟動的事件回應計畫對於説明客戶免受軟體安全或隱私漏洞的出現至關重要。

### <a name="monitor-application-performance"></a>監視應用程式的效能

部署應用程式後持續監視應用程式可能會説明您檢測性能問題以及安全性漏洞。
輔助應用程式監視的 Azure 服務包括：

  - Azure Application Insights
  - Azure 資訊安全中心

#### <a name="application-insights"></a>Application Insights

[應用程式見解](../../azure-monitor/app/app-insights-overview.md)是適用于多個平臺上的 Web 開發人員的可擴展應用程式性能管理 （APM） 服務。 您可以使用它來監視即時 Web 應用程式。 應用程式見解可自動檢測性能異常。 它包括強大的分析工具，可説明您診斷問題並瞭解使用者實際使用你的應用做什麼。 它是設計來協助您持續改善效能和可用性。

#### <a name="azure-security-center"></a>Azure 資訊安全中心

[Azure 安全中心](../../security-center/security-center-intro.md)通過提高 Azure 資源（包括 Web 應用程式）的安全性，説明您預防、檢測和回應威脅。 Azure 安全中心可説明檢測可能被忽視的威脅。 它適用于各種安全解決方案。

資訊安全中心的免費層僅為 Azure 資源提供有限的安全性。 [安全中心標準層](../../security-center/security-center-onboarding.md)將這些功能擴展到本地資源和其他雲。
安全中心標準可説明您：

  - 查找並修復安全性漏洞。
  - 應用訪問和應用程式控制項來阻止惡意活動。
  - 流量分析和智慧檢測威脅。
  - 受到攻擊時迅速回應。

## <a name="next-steps"></a>後續步驟
在以下文章中，我們建議安全控制和活動，以説明您設計和開發安全應用程式。

- [設計安全應用程式](secure-design.md)
- [開發安全的應用程式](secure-develop.md)
