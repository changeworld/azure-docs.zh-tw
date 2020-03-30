---
title: 計畫報告&監視部署 - Azure AD
description: 描述如何規劃和執行報告和監視的 impl。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232113"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>規劃 Azure 活動目錄報告和監視部署

Azure 活動目錄 （Azure AD） 報告和監視解決方案取決於法律、安全和操作要求以及現有環境和流程。 本文介紹了各種設計選項，並指導您制定正確的部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的優勢

Azure AD 報告提供了環境中 Azure AD 活動的全面視圖和日誌，包括登錄事件、審核事件和目錄更改。

提供的資料可讓您：

* 確定應用和服務的使用方式。

* 檢測影響環境健康的潛在風險。

* 排除問題，阻止使用者完成工作。

* 通過查看 Azure AD 目錄更改的審核事件來獲得見解。

> [!IMPORTANT]
> Azure AD 監視使您能夠將 Azure AD 報告生成的日誌路由到不同的目標系統。 您可以保留它以供長期使用，或將它與第三方安全性資訊與事件管理 (SIEM) 工具整合，以深入了解您的環境。

使用 Azure AD 監視，可以將日誌路由到：

* 用於存檔的 Azure 存儲帳戶。
* Azure 監視器日誌（以前稱為 Azure 日誌分析工作區），您可以在其中分析資料、創建儀表板並針對特定事件發出警報。
* Azure 事件中心，您可以在其中與現有的 SIEM 工具（如 Splunk、相撲或 QRadar）集成。

> [!NOTE]
我們最近開始使用術語 Azure 監視器日誌而不是日誌分析。 記錄資料仍儲存在 Log Analytics 工作區中，並仍由相同的 Log Analytics 服務收集和分析。 我們會持續更新術語，以更精確地反映 [Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)的角色。 如需詳細資料，請參閱 [Azure 監視器遙測變更](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)。

[瞭解有關報表保留原則的更多詳細資訊](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的許可和先決條件

您將需要 Azure AD 高級許可證才能訪問 Azure AD 登錄日誌。

有關[Azure 活動目錄定價指南](https://azure.microsoft.com/pricing/details/active-directory/)中的詳細資訊和許可資訊。

要部署 Azure AD 監視和報告，您需要一個使用者，該使用者是 Azure AD 租戶的全域管理員或安全管理員。

根據日誌資料的最終目的地，您需要以下之一：

* 您具有 ListKeys 權限的 Azure 儲存體帳戶。 建議您使用一般儲存體帳戶，而不要使用 Blob 儲存體帳戶。 如需儲存體定價資訊，請參閱 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 要與協力廠商 SIEM 解決方案集成的 Azure 事件中心命名空間。

* 用來將記錄傳送至 Azure 監視器記錄的 Azure Log Analytics 工作區。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>規劃 Azure 報告和監視部署專案

在此專案中，您將定義將用於監視報表的受眾群體，並定義 Azure AD 監視體系結構。

### <a name="engage-the-right-stakeholders"></a>讓合適的利益相關者參與進來

當技術專案失敗時，它們通常是由於對影響、結果和責任的期望不匹配而達到這一要求。 為了避免這些陷阱[，請確保您與合適的利益相關者接洽](https://aka.ms/deploymentplans)。 此外，通過記錄利益相關者及其專案投入和問責制，確保利益相關者在專案中的角色得到充分理解。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功都至關重要。 主動與使用者溝通他們的體驗將如何改變、何時更改，以及如果他們遇到問題如何獲得支援。

### <a name="document-your-current-infrastructure-and-policies"></a>記錄當前基礎架構和策略

您當前的基礎架構和政策將推動您的報告和監視設計。 確保您知道

* 您使用的 SIEM 工具（如果有）是什麼（

* Azure 基礎結構，包括正在使用的現有存儲帳戶和監視。

* 日誌的組織保留原則，包括所需的任何適用的合規性框架。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>規劃 Azure AD 報告和監視部署

報告和監視用於滿足您的業務需求、深入瞭解使用模式以及提高組織的安全狀況。

### <a name="business-use-cases"></a>業務用例

* 滿足業務需求所需的解決方案
* 滿足業務需求的好去向
* 不適用

|區域 |描述 |
|-|-|
|保留| **日誌保留超過30天**。 由於法律或業務需求，需要存儲稽核記錄並登錄 Azure AD 日誌的時間超過 30 天。 |
|分析| **日誌需要可搜索**。 存儲的日誌需要流量分析工具進行搜索。 |
| Operational Insights| **對不同團隊的見解**。 需要為不同的使用者提供存取權限，以獲得操作見解，如應用程式使用方式、登錄錯誤、自助服務使用、趨勢等。 |
| 安全洞察| **對不同團隊的見解**。 需要為不同的使用者提供存取權限，以獲得操作見解，如應用程式使用方式、登錄錯誤、自助服務使用、趨勢等。 |
| SIEM 系統集成      | **SIEM 集成**。 需要將 Azure AD 登錄日誌和稽核記錄集成到現有 SIEM 系統中。 |

### <a name="choose-a-monitoring-solution-architecture"></a>選擇監視解決方案體系結構

借助 Azure AD 監視，可以將 Azure AD 活動日誌路由到最能滿足業務需求的系統。 然後，您可以保留它們進行長期報告和分析，以便深入瞭解您的環境，並將其與 SIEM 工具集成。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>決策流程圖![顯示後續部分中描述的內容的圖像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>存儲帳戶中的存檔日誌

通過將日誌路由到 Azure 存儲帳戶，可以保留日誌的時間超過[保留原則](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)中概述的預設保留期。 如果需要存檔日誌，但不需要將它們與 SIEM 系統集成，並且不需要正在進行的查詢和分析，請使用此方法。 您仍然可以進行按需搜索。

了解如何[將資料路由傳送至儲存體帳戶](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)。

#### <a name="send-logs-to-azure-monitor-logs"></a>將記錄傳送至 Azure 監視器記錄

[Azure 監視器日誌](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)合併來自不同源的監視資料。 它還提供了查詢語言和分析引擎，使您能夠深入瞭解應用程式的操作和資源的使用。 通過將 Azure AD 活動日誌發送到 Azure 監視器日誌，可以快速檢索、監視和警報收集的資料。 如果沒有要將資料直接發送到但需要查詢和分析的現有 SIEM 解決方案，請使用此方法。 資料在 Azure 監視器日誌中後，可以將其發送到事件中心，如果需要，可以從那裡將其發送到 SIEM。

了解如何[將資料傳送至 Azure 監視器記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

還可以為 Azure AD 活動日誌安裝預構建的視圖，以監視涉及登錄和審核事件的常見方案。

瞭解如何[安裝和使用 Azure AD 活動日誌的日誌分析視圖](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>將日誌資料流到 Azure 事件中心

將日誌路由到 Azure 事件中心可與協力廠商 SIEM 工具集成。 此整合可讓您結合 Azure AD 活動記錄資料與 SIEM 所管理的其他資料，進而更深入了解您的環境。 

了解如何[將記錄串流至事件中樞](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>為 Azure AD 報告和監視規劃操作和安全性

專案關係人需要訪問 Azure AD 日誌才能獲得操作見解。 可能的使用者包括安全團隊成員、內部或外部審核員以及身份和訪問管理操作團隊。

Azure AD 角色使您能夠委派基於角色配置和查看 Azure AD 報表的能力。 確定組織中哪些人需要許可權才能讀取 Azure AD 報表，以及適合他們的角色。 

以下角色可以讀取 Azure AD 報表：

* 全域管理員

* 安全性系統管理員

* 安全性讀取者

* 報表讀取者

瞭解有關[Azure AD 管理角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)的更多詳細資訊。

*始終應用最低特權的概念，以降低帳戶洩露的風險*。 請考慮實施[特權身份管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)，以進一步保護您的組織。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 報告和監視

根據您之前使用上述設計指南所做的決策，本節將指導您瞭解有關不同部署選項的文檔。

### <a name="consume-and-archive-azure-ad-logs"></a>使用和存檔 Azure AD 日誌

[在 Azure 入口網站中尋找活動報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[將 Azure AD 日誌存檔到 Azure 存儲帳戶](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>實施監控和分析

[將日誌發送到 Azure 監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[安裝與使用適用於 Azure Active Directory 的記錄分析檢視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[使用 Azure 監視器記錄來分析 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [解釋 Azure 監視器中的稽核記錄架構](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [在 Azure 監視器中解釋登錄日誌架構](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [將 Azure AD 日誌資料流到 Azure 事件中心](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [使用 Azure 監視器將 Azure AD 記錄與 Splunk 整合](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [使用 Azure 監視器將 Azure AD 記錄與 SumoLogic 整合](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>後續步驟

考慮實施[特權身份管理](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

考慮實施[基於角色的存取控制 （RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
