---
title: 將您的警示從 Azure 資訊安全中心串流到安全性資訊和事件管理 (SIEM) 系統和其他監視解決方案
description: 瞭解如何將安全性警示串流至 Azure Sentinel、協力廠商 Siem、SOAR 或 ITSM 解決方案
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: b4458a2b37a3da83591e101344d08c3090868696
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341799"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>將警示串流至 SIEM、SOAR 或 IT 服務管理解決方案

Azure 資訊安全中心可以將安全性警示串流至最受歡迎的安全性資訊和事件管理 (SIEM) 、安全性協調流程自動化回應 (SOAR) ，以及 IT 服務管理 (ITSM) 解決方案。

Azure 原生工具可確保您可以在目前使用中的所有最熱門解決方案中查看警示資料，包括：

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **IBM 的 QRadar**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>將警示串流至 Azure Sentinel 

「安全性中心」原生整合了 Azure Sentinel、Azure 的雲端原生 SIEM 和 SOAR 解決方案。 

[深入瞭解 Azure Sentinel](../sentinel/overview.md)。

### <a name="azure-sentinels-connectors-for-security-center"></a>Azure Sentinel 適用于安全性中心的連接器

Azure Sentinel 包含訂用帳戶和租使用者層級 Azure 資訊安全中心的內建連接器：

- [將警示串流至訂用帳戶層級的 Azure Sentinel](../sentinel/connect-azure-security-center.md)
- [將您租使用者中的所有訂用帳戶連接到 Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>設定將所有 audit 記錄內嵌至 Azure Sentinel 

調查 Azure Sentinel 中的安全性中心警示的另一種替代方式是將您的 audit 記錄串流至 Azure Sentinel：
    - [連線 Windows 安全性事件](../sentinel/connect-windows-security-events.md)
    - [使用 Syslog 從以 Linux 為基礎的來源收集資料](../sentinel/connect-syslog.md)
    - [從 Azure 活動記錄連接資料](../sentinel/connect-azure-activity.md)

> [!TIP]
> Azure Sentinel 會根據 Azure Sentinel 的資料內嵌量進行計費，並儲存在 Azure 監視器 Log Analytics 工作區中。 Azure Sentinel 提供彈性且可預測的定價模型。 若要[深入瞭解，請參閱 Azure Sentinel 定價頁面](https://azure.microsoft.com/pricing/details/azure-sentinel/)。


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>使用 Microsoft Graph 安全性 API 來串流警示

「安全性中心」具有與 Microsoft Graph 安全性 API 的現成整合。 不需要進行任何設定，也不會有額外的成本。 

您可以使用此 API 來串流 **整個租** 使用者的警示 (以及許多其他 Microsoft 安全性產品) 至協力廠商 siem 和其他熱門平臺的資料：

- **Splunk Enterprise And Splunk Cloud**  - [針對 Splunk 使用 Microsoft Graph 安全性 API Add-On](https://splunkbase.splunk.com/app/4564/) 
- **Power BI**  - [連接到 Power BI Desktop 中的 Microsoft Graph 安全性 API](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  - [依照指示從 ServiceNow 存放區安裝和設定 Microsoft Graph 安全性 API 應用程式](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  - [IBM 的裝置支援模組，可透過 MICROSOFT GRAPH API 進行 Azure 資訊安全中心](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**、 **Anomali**、 **Lookout**、 **InSpark**和其他 [Microsoft Graph 安全性 API](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[深入瞭解 Microsoft Graph 安全性 API](https://www.microsoft.com/security/business/graph-security-api)。


## <a name="stream-alerts-with-azure-monitor"></a>使用 Azure 監視器來串流警示 

將警示串流至 **ArcSight**、 **Splunk**、 **SumoLogic**、Syslog Server、 **LogRhythm**、 **Logz.io Cloud 可檢視性 Platform**和其他監視解決方案。 透過 Azure 事件中樞將「安全性中心」與「Azure 監視器」連線：

1. 啟用 [連續匯出](continuous-export.md) ，以將安全性中心警示串流至訂用帳戶層級的專用 Azure 事件中樞。 
    > [!TIP]
    > 若要使用 Azure 原則在管理群組層級進行此操作，請參閱[大規模建立連續匯出自動化](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)設定

1. [使用 Azure 監視器的內建連接器，將 Azure 事件中樞連線到您偏好的解決方案](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)。

1. （選擇性）將未經處理的記錄串流至 Azure 事件中樞，並連接到您偏好的解決方案。 深入瞭解 [可用的監視資料](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#monitoring-data-available)。

> [!TIP]
> 若要查看已匯出資料類型的事件架構，請造訪 [事件中樞事件架構](https://aka.ms/ASCAutomationSchemas)。


## <a name="next-steps"></a>後續步驟

本頁說明如何確保您的 SIEM、SOAR 或 ITSM 工具中有提供您 Azure 資訊安全中心的警示資料。 如需相關材質，請參閱：

- [什麼是 Azure Sentinel？](../sentinel/overview.md)
- [Azure 資訊安全中心中的警示驗證](security-center-alert-validation.md) -確認已正確設定您的警示
- [持續匯出安全性警示和建議](continuous-export.md)