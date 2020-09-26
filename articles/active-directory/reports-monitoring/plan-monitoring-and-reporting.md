---
title: 規劃 & 監視部署的報表-Azure AD
description: 說明如何規劃和執行報告和監視的執行。
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
ms.openlocfilehash: cf8ceb308fbcc2156a6df43e0167f4cb544b2d3d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331194"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>規劃 Azure Active Directory 報告和監視部署

您的 Azure Active Directory (Azure AD) 報告和監視解決方案，取決於您的法律、安全性和操作需求，以及您現有的環境和程式。 本文提供各種設計選項，並引導您進行適當的部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的優點

Azure AD 報告提供您環境中 Azure AD 活動的完整觀點和記錄，包括登入事件、審核事件，以及對目錄所做的變更。

提供的資料可讓您：

* 判斷您的應用程式和服務的使用方式。

* 偵測影響環境健康情況的潛在風險。

* 針對防止使用者完成其工作的問題進行疑難排解。

* 查看 Azure AD 目錄變更的 audit 事件，以深入瞭解。

> [!IMPORTANT]
> Azure AD 監視功能可讓您將 Azure AD 報告產生的記錄路由至不同的目標系統。 您可以保留它以供長期使用，或將它與第三方安全性資訊與事件管理 (SIEM) 工具整合，以深入了解您的環境。

使用 Azure AD 監視時，您可以將記錄路由至：

* 用於封存用途的 Azure 儲存體帳戶。
* Azure 監視器記錄（先前稱為 Azure Log Analytics 工作區），您可以在其中分析資料、建立儀表板，以及針對特定事件發出警示。
* Azure 事件中樞，您可以在其中整合現有的 SIEM 工具，例如 Splunk、Sumologic 或 QRadar。

> [!NOTE]
我們最近開始使用 Azure 監視器記錄的詞彙，而不是 Log Analytics。 記錄資料仍儲存在 Log Analytics 工作區中，並仍由相同的 Log Analytics 服務收集和分析。 我們會持續更新術語，以更精確地反映 [Azure 監視器記錄](../../azure-monitor/platform/data-platform.md)的角色。 如需詳細資料，請參閱 [Azure 監視器遙測變更](../../azure-monitor/terminology.md)。

[深入瞭解報告保留原則](./reference-reports-data-retention.md)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的授權和必要條件

您將需要 Azure AD premium 授權，才能存取 Azure AD 登入記錄。

如需詳細的功能和授權資訊，請 [Azure Active Directory 定價指南](https://azure.microsoft.com/pricing/details/active-directory/)。

若要部署 Azure AD 監視和報告，您需要具有 Azure AD 租使用者之全域管理員或安全性系統管理員的使用者。

根據記錄資料的最終目的地，您需要下列其中一項：

* 您具有 ListKeys 權限的 Azure 儲存體帳戶。 建議您使用一般儲存體帳戶，而不要使用 Blob 儲存體帳戶。 如需儲存體價格資訊，請參閱 [Azure 儲存體價格計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 要與協力廠商 SIEM 解決方案整合的 Azure 事件中樞命名空間。

* 用來將記錄傳送至 Azure 監視器記錄的 Azure Log Analytics 工作區。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>規劃 Azure 報告和監視部署專案

在此專案中，您將定義將取用和監視報表的物件，並定義您的 Azure AD 監視架構。

### <a name="engage-the-right-stakeholders"></a>包含正確的專案關係人

當技術專案失敗時，其通常是因為人員對影響、結果與責任抱持不相符的預期而造成。 若要避免這些問題，請 [確定您正參與適當的專案關係人](https://aka.ms/deploymentplans)。 此外，也請記錄專案關係人及其專案輸入和責任，以確定專案中的專案關係人角色都能充分瞭解。

### <a name="plan-communications"></a>規劃通訊

溝通對於任何新服務的成功非常重要。 主動與您的使用者溝通其體驗將如何改變、何時會改變，以及如何在遇到問題時取得支援。

### <a name="document-your-current-infrastructure-and-policies"></a>記錄您目前的基礎結構和原則

您目前的基礎結構和原則將會驅動您的報告和監視設計。 確定您知道

* 您所使用的 SIEM 工具（如果有的話）。

* 您的 Azure 基礎結構，包括現有的儲存體帳戶和使用中的監視。

* 您組織的記錄保留原則，包括所需的任何適用的合規性架構。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>規劃 Azure AD 報告和監視部署

報告和監視是用來滿足您的業務需求、深入瞭解使用模式，以及增加您組織的安全性狀態。

### <a name="business-use-cases"></a>商務使用案例

* 符合商務需求的解決方案所需
* 滿足商務需求的理想
* 不適用

|區域 |說明 |
|-|-|
|保留| 超過**30 天的記錄保留期**。 由於法律或業務需求，需要儲存 Azure AD 超過30天的審核記錄和登入記錄。 |
|分析| **記錄檔必須是可搜尋的**。 儲存的記錄必須可流量分析工具來搜尋。 |
| Operational Insights| **適用于各種小組的見解**。 需要為不同的使用者提供存取權，以取得作業見解，例如應用程式使用狀況、登入錯誤、自助使用狀況、趨勢等等。 |
| 安全性見解| **適用于各種小組的見解**。 需要為不同的使用者提供存取權，以取得作業見解，例如應用程式使用狀況、登入錯誤、自助使用量、趨勢等等。 |
| SIEM 系統中的整合      | **SIEM 整合**。 需要整合和串流 Azure AD 登入記錄和審核記錄到現有的 SIEM 系統。 |

### <a name="choose-a-monitoring-solution-architecture"></a>選擇監視解決方案架構

使用 Azure AD 監視時，您可以將 Azure AD 活動記錄路由傳送至最符合您商務需求的系統。 然後您可以保留它們進行長期報告和分析，以深入瞭解您的環境，並將其與 SIEM 工具整合。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>決策流程圖![顯示後續章節所述內容的影像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>保存儲存體帳戶中的記錄

藉由將記錄路由傳送至 Azure 儲存體帳戶，您可以將其保留超過 [保留原則](./reference-reports-data-retention.md)中所述的預設保留週期。 如果您需要封存記錄，但不需要將它們與 SIEM 系統整合，且不需要進行中的查詢和分析，請使用此方法。 您仍然可以進行隨選搜尋。

了解如何[將資料路由傳送至儲存體帳戶](./quickstart-azure-monitor-route-logs-to-storage-account.md)。

#### <a name="send-logs-to-azure-monitor-logs"></a>將記錄傳送至 Azure 監視器記錄

[Azure 監視器記錄](../../azure-monitor/log-query/log-query-overview.md) 會合並來自不同來源的監視資料。 它也提供查詢語言和分析引擎，可讓您深入瞭解應用程式的運作和使用資源。 藉由將 Azure AD 活動記錄傳送到 Azure 監視器記錄檔，您可以快速取得、監視及警示所收集的資料。 如果您沒有想要直接將資料傳送至其中的現有 SIEM 方案，但需要查詢和分析，請使用此方法。 當您的資料位於 Azure 監視器記錄檔中之後，您就可以將它傳送至事件中樞，並在需要時將其傳送至 SIEM。

了解如何[將資料傳送至 Azure 監視器記錄](./howto-integrate-activity-logs-with-log-analytics.md)。

您也可以安裝 Azure AD 活動記錄的預先建立的視圖，以監視涉及登入和審核事件的常見案例。

了解如何[安裝與使用適用於 Azure AD 活動記錄的記錄分析檢視](./howto-install-use-log-analytics-views.md)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>將記錄串流至您的 Azure 事件中樞

將記錄路由傳送至 Azure 事件中樞，可與協力廠商 SIEM 工具整合。 此整合可讓您結合 Azure AD 活動記錄資料與 SIEM 所管理的其他資料，進而更深入了解您的環境。 

了解如何[將記錄串流至事件中樞](/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>規劃 Azure AD 報告和監視的作業和安全性

專案關係人需要存取 Azure AD 記錄以取得 operational insights。 可能的使用者包括安全性團隊成員、內部或外部審核員，以及身分識別與存取管理作業小組。

Azure AD 角色可讓您根據您的角色委派設定和查看 Azure AD 報表的能力。 識別您組織中的誰需要讀取 Azure AD 報表的許可權，以及這些報表適合的角色。 

下列角色可以讀取 Azure AD 報表：

* 全域管理員

* 安全性系統管理員

* 安全性讀取者

* 報告讀者

深入瞭解 [Azure AD 系統管理角色](../users-groups-roles/directory-assign-admin-roles.md)。

請*一律套用最低許可權的概念，以降低帳戶入侵的風險*。 請考慮實施 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) ，以進一步保護您的組織。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 報告和監視

根據您先前使用上述設計指引所做的決策，本節將引導您使用不同部署選項的相關檔。

### <a name="consume-and-archive-azure-ad-logs"></a>使用和封存 Azure AD 記錄

[在 Azure 入口網站中尋找活動報告](./howto-find-activity-reports.md)

[將 Azure AD 記錄封存至 Azure 儲存體帳戶](./quickstart-azure-monitor-route-logs-to-storage-account.md)

### <a name="implement-monitoring-and-analytics"></a>實行監視和分析

[將記錄傳送給 Azure 監視器](./howto-integrate-activity-logs-with-log-analytics.md)

[安裝與使用適用於 Azure Active Directory 的記錄分析檢視](./howto-install-use-log-analytics-views.md)

[使用 Azure 監視器記錄來分析 Azure AD 活動記錄](./howto-analyze-activity-logs-log-analytics.md)

* [解譯 Azure 監視器中的稽核記錄結構描述](./reference-azure-monitor-audit-log-schema.md)

* [在 Azure 監視器中解讀登入記錄架構](./reference-azure-monitor-sign-ins-log-schema.md)

 * [將 Azure AD 記錄串流至 Azure 事件中樞](./tutorial-azure-monitor-stream-logs-to-event-hub.md)

* [使用 Azure 監視器將 Azure AD 記錄與 Splunk 整合](./howto-integrate-activity-logs-with-splunk.md)

* [使用 Azure 監視器將 Azure AD 記錄與 SumoLogic 整合](./howto-integrate-activity-logs-with-sumologic.md)

 

 

## <a name="next-steps"></a>後續步驟

考慮實施 [Privileged Identity Management](../privileged-identity-management/pim-configure.md) 

請考慮在 [AZURE RBAC)  (執行 azure 角色型存取控制 ](../../role-based-access-control/overview.md)
