---
title: 規劃報表 & 監視部署-Azure AD
description: 說明如何規劃和執行報告和監視的 implmentation。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "74232113"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>規劃 Azure Active Directory 報告和監視部署

您的 Azure Active Directory （Azure AD）報告和監視解決方案取決於您的法律、安全性和營運需求，以及現有的環境和程式。 本文提供各種不同的設計選項，並引導您進行適當的部署策略。

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的優點

Azure AD 報告提供環境中 Azure AD 活動的完整視圖和記錄，包括登入事件、audit 事件和您目錄的變更。

提供的資料可讓您：

* 決定您的應用程式和服務的使用方式。

* 偵測影響環境健全狀況的潛在風險。

* 針對阻止使用者完成其工作的問題進行疑難排解。

* 查看 Azure AD 目錄變更的 audit 事件以取得深入資訊。

> [!IMPORTANT]
> Azure AD 監視可讓您將 Azure AD 報告所產生的記錄路由傳送至不同的目標系統。 您可以保留它以供長期使用，或將它與第三方安全性資訊與事件管理 (SIEM) 工具整合，以深入了解您的環境。

透過 Azure AD 監視，您可以將記錄路由至：

* 供封存之用的 Azure 儲存體帳戶。
* Azure 監視器記錄，先前稱為 Azure Log Analytics 工作區，您可以在其中分析資料、建立儀表板，以及針對特定事件發出警示。
* Azure 事件中樞，您可以在其中整合現有的 SIEM 工具，例如 Splunk、Sumologic 或 QRadar。

> [!NOTE]
我們最近開始使用「Azure 監視器記錄」一詞，而不是 Log Analytics。 記錄資料仍儲存在 Log Analytics 工作區中，並仍由相同的 Log Analytics 服務收集和分析。 我們會持續更新術語，以更精確地反映 [Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection)的角色。 如需詳細資料，請參閱 [Azure 監視器遙測變更](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand)。

[深入瞭解報告保留原則](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)。

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Azure AD 報告和監視的授權和必要條件

您將需要 Azure AD premium 授權，才能存取 Azure AD 登入記錄。

如需詳細的功能和授權資訊，請[Azure Active Directory 定價指南](https://azure.microsoft.com/pricing/details/active-directory/)。

若要部署 Azure AD 監視和報告，您必須是 Azure AD 租使用者的全域管理員或安全性系統管理員的使用者。

視記錄資料的最終目的地而定，您需要下列其中一項：

* 您具有 ListKeys 權限的 Azure 儲存體帳戶。 建議您使用一般儲存體帳戶，而不要使用 Blob 儲存體帳戶。 如需儲存體定價資訊，請參閱 [Azure 儲存體定價計算機](https://azure.microsoft.com/pricing/calculator/?service=storage)。

* 要與協力廠商 SIEM 解決方案整合的 Azure 事件中樞命名空間。

* 用來將記錄傳送至 Azure 監視器記錄的 Azure Log Analytics 工作區。

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>規劃 Azure reporting 和 monitoring 部署專案

在此專案中，您將定義將取用和監視報表的物件，以及定義您的 Azure AD 監視架構。

### <a name="engage-the-right-stakeholders"></a>參與適當的專案關係人

當技術專案失敗時，通常是因為對影響、結果和責任的預期不相符所造成。 若要避免這些錯誤，請[確定您參與的是正確的專案關係人](https://aka.ms/deploymentplans)。 記錄專案關係人和專案的輸入和標準責任，以確保可以充分瞭解專案中的專案關係人角色。

### <a name="plan-communications"></a>規劃溝通

通訊對於任何新服務的成功非常重要。 主動與您的使用者溝通他們的體驗會如何變更，變更時，以及如何在遇到問題時取得支援。

### <a name="document-your-current-infrastructure-and-policies"></a>記錄您目前的基礎結構和原則

您目前的基礎結構和原則將會驅動您的報告和監視設計。 確定您知道

* 您所使用的 SIEM 工具（如果有的話）。

* 您的 Azure 基礎結構，包括現有的儲存體帳戶和使用中的監視。

* 記錄的組織保留原則，包括所需的任何適用的合規性架構。 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>規劃 Azure AD 報告和監視部署

報告和監視是用來滿足您的商務需求、深入瞭解使用模式，並不會增加組織的安全性狀態。

### <a name="business-use-cases"></a>商務使用案例

* 符合商務需求的解決方案所需
* 符合業務需求的絕佳想法
* 不適用

|區域 |描述 |
|-|-|
|保留| **記錄保留期限超過30天**。 由於法律或商務需求，因此必須儲存 audit 記錄和登入 Azure AD 超過30天的記錄。 |
|分析| **記錄必須可**供搜尋。 儲存的記錄檔必須可流量分析工具進行搜尋。 |
| Operational Insights| **適用于各種小組的深入**解析。 需要為不同的使用者提供存取權，以取得諸如應用程式使用量、登入錯誤、自助使用狀況、趨勢等的操作見解。 |
| 安全性見解| **適用于各種小組的深入**解析。 需要為不同的使用者提供存取權，以取得諸如應用程式使用量、登入錯誤、自助使用狀況、趨勢等的操作見解。 |
| SIEM 系統中的整合      | **SIEM 整合**。 需要整合和串流 Azure AD 登入記錄檔，並將記錄檔寫入現有的 SIEM 系統。 |

### <a name="choose-a-monitoring-solution-architecture"></a>選擇監視解決方案架構

透過 Azure AD 監視，您可以將 Azure AD 活動記錄路由至最符合您業務需求的系統。 然後您可以保留它們以進行長期報告和分析，以深入瞭解您的環境，並將其與 SIEM 工具整合。

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>決策流程圖表![顯示後續章節所述內容的影像](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>封存儲存體帳戶中的記錄

藉由將記錄路由傳送至 Azure 儲存體帳戶，您可以將它們保留超過[保留原則](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention)中所述的預設保留週期。 如果您需要封存您的記錄，但不需要將它們與 SIEM 系統整合，而不需要進行中的查詢和分析，請使用此方法。 您仍然可以執行隨選搜尋。

了解如何[將資料路由傳送至儲存體帳戶](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)。

#### <a name="send-logs-to-azure-monitor-logs"></a>將記錄傳送至 Azure 監視器記錄

[Azure 監視器記錄](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)會合並來自不同來源的監視資料。 它也提供查詢語言和分析引擎，可讓您深入瞭解應用程式的操作和資源的使用。 藉由將 Azure AD 活動記錄傳送至 Azure 監視器記錄，您可以快速地抓取、監視和警示所收集的資料。 當您沒有想要直接將資料傳送至其中的現有 SIEM 解決方案，但想要查詢和分析時，請使用這個方法。 當您的資料位於 Azure 監視器記錄檔之後，您就可以將它傳送至事件中樞，並視需要將它傳送至 SIEM。

了解如何[將資料傳送至 Azure 監視器記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

您也可以為 Azure AD 活動記錄安裝預先建立的視圖，以監視涉及登入和審核事件的常見案例。

瞭解如何[安裝和使用 log analytics views 來進行 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)。

#### <a name="stream-logs-to-your-azure-event-hub"></a>將記錄串流至您的 Azure 事件中樞

將記錄路由傳送至 Azure 事件中樞，可讓您與協力廠商 SIEM 工具整合。 此整合可讓您結合 Azure AD 活動記錄資料與 SIEM 所管理的其他資料，進而更深入了解您的環境。 

了解如何[將記錄串流至事件中樞](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)。

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>規劃 Azure AD 報告和監視的作業和安全性

專案關係人需要存取 Azure AD 記錄以取得 operational insights。 可能的使用者包括安全性小組成員、內部或外部審計員，以及身分識別和存取管理作業小組。

Azure AD 角色可讓您根據您的角色，委派設定及查看 Azure AD 報告的功能。 識別您組織中的誰需要讀取 Azure AD 報表的許可權，以及適當的角色。 

下列角色可以讀取 Azure AD 報表：

* 全域管理員

* 安全性系統管理員

* 安全性讀取者

* 報表讀取者

深入瞭解[Azure AD 系統管理角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)。

請*一律套用最低許可權的概念，以降低帳戶洩露的風險*。 請考慮執行[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)以進一步保護您的組織。

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>部署 Azure AD 報告和監視

根據您先前使用上述設計指引所做的決策，本節將引導您進行不同部署選項的相關檔。

### <a name="consume-and-archive-azure-ad-logs"></a>使用和封存 Azure AD 記錄

[在 Azure 入口網站中尋找活動報告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[將 Azure AD 記錄封存到 Azure 儲存體帳戶](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>執行監視和分析

[將記錄傳送至 Azure 監視器](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[安裝與使用適用於 Azure Active Directory 的記錄分析檢視](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[使用 Azure 監視器記錄來分析 Azure AD 活動記錄](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [解讀 Azure 監視器中的 audit 記錄架構](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [解讀 Azure 監視器中的登入記錄架構](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [將 Azure AD 記錄串流至 Azure 事件中樞](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [使用 Azure 監視器將 Azure AD 記錄與 Splunk 整合](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [使用 Azure 監視器將 Azure AD 記錄與 SumoLogic 整合](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>後續步驟

請考慮執行[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

請考慮執行[角色型存取控制（RBAC）](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
