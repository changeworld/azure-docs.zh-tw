---
title: 使用 Azure AD B2C 進行監視和分析的復原能力 |Microsoft Docs
description: 使用 Azure AD B2C 進行監視和分析的復原能力
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 766fd80ae5f7450c8e45d10afa4612a788a8d5fc
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602151"
---
# <a name="resilience-through-monitoring-and-analytics"></a>透過監視及分析的復原能力

監視會將應用程式和服務的可用性和效能最大化。 它提供了一套完整的解決方案，可從您的基礎結構和應用程式收集、分析及處理遙測資料。 當您的服務或應用程式發現問題時，警示會主動通知您。 它們可讓您在服務的終端使用者注意到問題之前，識別並解決問題。 [Azure AD Log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) 可協助您分析、搜尋審核記錄和登入記錄，以及建立自訂的視圖。

## <a name="monitor-and-get-notified-through-alerts"></a>透過警示監視並取得通知

監視您的系統和基礎結構對於確保服務的整體健全狀況至關重要。 它會從商務計量的定義開始，例如新的使用者抵達、終端使用者的驗證率和轉換。 設定這類指標以進行監視。 如果您正在規劃即將推出的激增（因為促銷或假日流量），請特別針對事件和對應的商務計量基準來修訂您的估價。 在事件之後，回到先前的基準測試。

同樣地，若要偵測失敗或效能中斷，設定良好的基準，然後定義警示是立即回應新興問題的不可或缺做法。

![顯示監視和分析元件的影像](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>如何實行監視和警示

- **監視**：使用 [Azure 監視器](https://docs.microsoft.com/azure/active-directory-b2c/azure-monitor) 可針對 (SLO) 的重要服務等級目標持續監視健全狀況，並在發生重大變更時取得通知。 首先，將 Azure AD B2C 原則或應用程式識別為您企業中的重要元件，其健康情況需要監視以維護 SLO。 識別與 Slo 一致的關鍵指標。
例如，追蹤下列計量，因為其中的突然下降會導致企業遺失。

  - **要求總數**：傳送給 Azure AD B2C 原則的要求數總計「n」。

  - **成功率 (% )**：成功的要求數/要求總數。

  存取[application insights](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights)中 Azure AD B2C 以原則為基礎的記錄、[審核記錄](https://docs.microsoft.com/azure/active-directory-b2c/analytics-with-application-insights)和登入記錄檔的[重要](https://docs.microsoft.com/azure/active-directory-b2c/view-audit-logs)指標。  

   - 視覺 **效果**：使用 Log analytics 組建儀表板以視覺化方式監視關鍵指標。

   - **目前期間**：建立時態圖以顯示目前期間內 (% ) 的總要求和成功率的變更，例如，目前的周。

   - **上一期**：建立時態圖，以顯示過去一段之前的要求和成功率 (% ) 的變更，例如上周。

- **警示**：使用 log analytics 會定義在關鍵指標突然變更時所觸發的 [警示](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) 。 這些變更可能會對 Slo 造成負面影響。 警示會使用各種形式的通知方法，包括電子郵件、SMS 和 webhook。 首先，定義可作為閾值的準則，以觸發警示。 例如：
  - 警示要求總數突然下降：當總要求數突然下降時觸發警示。 例如，相較于前一個期間，如果要求總數中有25% 的下降，則會引發警示。  
  - 針對成功率下降 (% ) 的警示：當所選原則的成功率大幅下降時，觸發警示。
  - 收到警示時，請使用 [Log Analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)、 [Application Insights](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-with-application-insights#:~:text=Setup%20Application%20Insights%201%20Go%20to%20the%20Azure,left-menu%2C%20and%20click%20on%20it.%20More%20items...%20)和 [VS Code 擴充](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) 功能，針對 Azure AD B2C 進行問題的疑難排解。 解決問題並部署更新的應用程式或原則之後，它會繼續監視關鍵指標，直到它們回到正常的範圍為止。

- **服務警示**：使用 [Azure AD B2C 服務等級的警示](https://docs.microsoft.com/azure/service-health/service-health-overview) 來取得服務問題、規劃的維護、健康情況諮詢和安全性諮詢的通知。

- **報告**： [使用 log analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)可建立報告，以協助您深入瞭解使用者見解、技術挑戰和成長商機。
  - **健康情況儀表板**： [使用 Azure 儀表板功能建立自訂儀表板](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards) ，其支援使用 Log Analytics 查詢來新增圖表。 例如，識別成功和失敗登入的模式、失敗原因，以及用於提出要求之裝置的遙測。
  - **放棄 Azure AD B2C 旅程**：使用活頁 [簿](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) 來追蹤使用者開始登入或註冊旅程，但從未完成的已放棄 Azure AD B2C 旅程清單。 它會提供使用者在放棄旅程之前所採取之步驟的原則識別碼和明細詳細資料。
  - **Azure AD B2C 監視活頁簿**：使用 [監視活頁簿](https://github.com/azure-ad-b2c/siem)，包括 Azure AD B2C 儀表板、多重要素驗證 (MFA) 作業、條件式存取報表，以及依 correlationId 的搜尋記錄，以深入瞭解您的 Azure AD B2C 環境的健全狀況。
  
## <a name="next-steps"></a>後續步驟

- [Azure AD B2C 開發人員的復原能力資源](resilience-b2c.md)
  - [復原的終端使用者體驗](resilient-end-user-experience.md)
  - [具有外部進程的復原介面](resilient-external-processes.md)
  - [透過開發人員最佳作法的復原能力](resilience-b2c-developer-best-practices.md)
- [在您的驗證基礎結構中建立恢復功能](resilience-in-infrastructure.md)
- [提高應用程式中驗證和授權的復原能力](resilience-app-development-overview.md)
