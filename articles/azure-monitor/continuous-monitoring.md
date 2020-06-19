---
title: 使用 Azure 監視器持續監視 | Microsoft Docs
description: 描述使用 Azure 監視器在整個工作流程中啟用持續監視的特定步驟。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/12/2018
ms.openlocfilehash: 6df27dde997bd34e86b1bb340817648bfe68f2c4
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797608"
---
# <a name="continuous-monitoring-with-azure-monitor"></a>使用 Azure 監視器持續監視

持續監視是指在 DevOps 和 IT 作業生命週期的每個階段中，包含監視所需的程序與技術。 隨著持續監視從開發移至生產環境，它有助於持續確保應用程式和基礎結構的健全狀況、效能和可靠性。 持續監視以持續整合與持續部署 (CI/CD) 的概念為基礎，可幫助您更快、更可靠地開發和提供軟體，為您的使用者持續提供價值。

[Azure 監視器](overview.md)是 Azure 中的統一監視解決方案，可在雲端和內部部署中，跨應用程式和基礎結構提供完整堆疊可檢視性。 它在開發與測試期間與 [Visual Studio 和 Visual Studio Code](https://visualstudio.microsoft.com/) 完美搭配，並與 [Azure DevOps](/azure/devops/user-guide/index) 整合，以便在部署和作業期間進行版本管理和工作項目管理。 它甚至可以整合您選擇的 ITSM 和 SIEM 工具，以協助追蹤現有 IT 程序中的問題和事件。

本文說明使用 Azure 監視器在整個工作流程中啟用持續監視的特定步驟。 它包含指向其他文件的連結，其中提供了實作不同功能的詳細資料。


## <a name="enable-monitoring-for-all-your-applications"></a>為所有應用程式啟用監視
若要取得整個環境的可檢視性，您需要對所有 Web 應用程式和服務啟用監視。 這可讓您輕鬆地將跨所有元件的端對端交易和連線以視覺化方式呈現。

- [Azure DevOps Projects](../devops-project/overview.md) 為您提供了現有程式碼和 Git 存放庫的簡化體驗，或者從其中一個範例應用程式中進行選擇，以在 Azure 中建立持續整合 (CI) 和持續傳遞 (CD) 管線。
- [DevOps 發行管線中的持續監視](../azure-monitor/app/continuous-monitoring.md)允許您根據監視資料對部署進行閘道設定或復原。
- [狀態監視器](../azure-monitor/app/monitor-performance-live-website-now.md)允許您使用 Azure Application Insights 在 Windows 上檢測即時 .NET 應用程式，而不需修改或重新部署您的程式碼。
- 如果您有權存取應用程式的程式碼，則透過為[.NET](../azure-monitor/learn/quick-monitor-portal.md)、[Java](../azure-monitor/app/java-get-started.md)、[Node.js](../azure-monitor/learn/nodejs-quick-start.md) 或[任何其他程式設計語言](../azure-monitor/app/platforms.md)安裝 Azure Monitor Application Insights SDK，啟用 [Application Insights](../azure-monitor/app/app-insights-overview.md) 的完全監視。 這允許您指定與您的應用程式和業務相關的自訂事件、計量或頁面檢視。



## <a name="enable-monitoring-for-your-entire-infrastructure"></a>為整個基礎結構啟用監視
應用程式僅與其基礎結構一樣可靠。 在整個基礎結構中啟用監視將協助您達成完整的可檢視性，並在發生故障時更容易發現潛在的根本原因。 Azure 監視器可協助您追蹤整個混合式基礎結構的健全狀況和效能，包括 VM、容器、儲存體和網路等資源。

- 您可以從大多數 Azure 資源中自動取得[平台計量、活動記錄和診斷記錄](platform/data-sources.md)，而無需進行任何設定。
- 使用[適用於 VM 的 Azure 監視器](insights/vminsights-overview.md)，針對 VM 啟用更深入的監視。
-  使用[適用於容器的 Azure 監視器](insights/container-insights-overview.md)，針對 AKS 叢集啟用更深入的監視。
- 新增環境中不同應用程式與服務的[監視解決方案](insights/solutions-inventory.md)。


[基礎結構即程式碼](/azure/devops/learn/what-is-infrastructure-as-code)是描述性模型中基礎結構的管理，使用與 DevOps 小組針對原始程式碼所使用的相同版本設定。 它為您的環境增加了可靠性和延展性，並允許您利用針對管理應用程式所使用的類似程序。

-  使用 [Resource Manager 範本](platform/template-workspace-configuration.md)啟用對大量資源的監視並設定警示。
- 使用 [Azure 原則](../governance/policy/overview.md)來對您的資源強制執行不同的規則。 這樣會確保那些資源會符合您的公司標準及服務等級協定規範。 


##  <a name="combine-resources-in-azure-resource-groups"></a>在 Azure 資源群組中合併資源
Azure 上的一般應用程式包含多個資源，例如 VM 和應用程式服務或裝載於雲端服務、AKS 叢集中或 Service Fabric 上的微服務。 這些應用程式經常會利用像是事件中樞、儲存體、SQL 和服務匯流排等相依性。

- 結合 Azure 資源群組中的資源，以完整了解組成不同應用程式的所有資源。 [適用於資源群組的 Azure 監視器](../azure-monitor/insights/resource-group-insights.md)提供了一種簡單的方式，來追蹤整個完整堆疊應用程式的健全狀況和效能，並可以向下切入個別元件以進行任何調查或偵錯。

## <a name="ensure-quality-through-continuous-deployment"></a>通過持續部署確保品質
持續整合 / 持續部署允許您根據自動化測試的結果，將程式碼變更自動整合及部署到您的應用程式。 它可簡化部署程序，並在進入生產階段前確保任何變更的品質。


- 使用 [Azure 管線](/azure/devops/pipelines)來實作持續部署，並根據您的 CI/CD 測試，自動化從程式碼認可到生產環境的整個程序。
- 使用[品質閘門](/azure/devops/pipelines/release/approvals/gates)將監視整合到部署前或部署後。 這可確保您在應用程式從開發階段移至生產環境時符合主要的健康情況/效能計量 (KPI)，並且基礎結構環境或規模中的任何差異都不會對您的 KPI 造成負面影響。
- 在不同的部署環境 (如 Dev、Test，Canary 和 Prod) 之間[維護個別的監視執行個體](../azure-monitor/app/separate-resources.md)。這可確保收集的資料與相關聯的應用程式和基礎結構相關。 如果需要跨環境將資料相互關聯，可以[在計量瀏覽器中使用多資源圖表](../azure-monitor/platform/metrics-charts.md)，或[在 Azure 監視器中建立跨資源查詢](log-query/cross-workspace-query.md)。


## <a name="create-actionable-alerts-with-actions"></a>使用動作建立可採取動作的警示
監視的一個重要層面是主動向系統管理員通知任何目前的問題和已預測的問題。 

- 根據記錄和計量[在 Azure 監視器中建立警示](../azure-monitor/platform/alerts-overview.md)，以識別可預測的失敗狀態。 您的目標應該是讓所有的警示可採取動作，這表示它們代表實際的重大情況，並設法減少誤判。 使用[動態閾值](platform/alerts-dynamic-thresholds.md)即可自動計算計量資料的基準，而不是定義您自己的靜態閾值。 
- 定義警示的動作，以使用最有效的方式通知您的系統管理員。 [通知的可用動作](platform/action-groups.md#create-an-action-group-by-using-the-azure-portal)包括 SMS、電子郵件、推播通知，或語音通話。
- 使用更進階的動作透過 [webhook](platform/activity-log-alerts-webhook.md)[連接到您的 ITSM 工具](platform/itsmc-overview.md)或其他警示管理系統。
- 修復警示中所識別的情況，以及可以使用 webhook 從警示啟動的 [Azure 自動化 runbook](../automation/automation-webhooks.md) 或 [Logic Apps](/connectors/custom-connectors/create-webhook-trigger)。 
- 根據所收集的計量，使用[自動調整](../azure-monitor/learn/tutorial-autoscale-performance-schedule.md)來動態增加和減少計算資源。

## <a name="prepare-dashboards-and-workbooks"></a>準備儀表板和活頁簿
確保您的開發和作業可以存取相同的遙測資料和工具，使它們能夠檢視整個環境中的模式，並將平均偵測時間 (MTTD) 和平均還原時間 (MTTR)　降到最低。

- 根據組織中不同角色的常用計量和記錄準備[自訂儀表板](../azure-monitor/learn/tutorial-app-dashboards.md)。 儀表板可以結合來自所有 Azure 資源的資料。
- 準備[活頁簿](../azure-monitor/platform/workbooks-overview.md)以確保開發與作業之間的知識共享。 這些可以準備為包含計量圖表和記錄查詢的動態報告，或者甚至作為開發人員準備的疑難排解指引，協助客戶支援或作業處理基本問題。

## <a name="continuously-optimize"></a>持續最佳化
 監視是熱門「建置-測量-學習」理念的基本層面之一，其建議持續追蹤您的 KPI 和使用者行為計量，然後透過計劃反覆項目來努力最佳化 KPI。 Azure 監視器可協助您收集與業務相關的計量和記錄，並根據需要在下一個部署中新增新的資料點。

- 使用 Application Insights 中的工具[追蹤使用者行為和參與度](../azure-monitor/learn/tutorial-users.md)。
- 使用[影響分析](../azure-monitor/app/usage-impact.md)來協助您排定要關注哪些領域的優先順序，以推動重要的 KPI。


## <a name="next-steps"></a>後續步驟

- 了解 [Azure 監視器](overview.md)的差異元件。
- [將連續監視新增至您的發行管線](../azure-monitor/app/continuous-monitoring.md)。
