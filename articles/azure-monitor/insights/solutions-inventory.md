---
title: Azure 中的監視解決方案清單 |微軟文檔
description: Azure 監視器中的監視解決方案是邏輯、視覺效果和資料擷取規則的集合，可提供針對特定問題領域進行計量的樞紐分析。  本文提供了 Microsoft 提供的監視解決方案的清單，以及有關其資料收集方法和頻率的詳細資訊。
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663124"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>用於監視 Azure 中解決方案的清單和資料收集詳細資訊
[監視解決方案](solutions.md)利用 Azure 中的服務來提供對特定應用程式或服務操作的其他見解。 監視解決方案通常會收集記錄資料，並提供查詢和檢視來分析所收集的資料。 您可以針對您使用的任何應用程式和服務，將監視解決方案新增至 Azure 監視器。 這些管理解決方案通常免費提供，但是會收集可能造成使用費用的資料。

本文包括 Microsoft 提供的[蒙太林解決方案](solutions.md)清單，其中包含指向其詳細文檔的連結。  此外，還提供資料收集到 Azure 監視器中的方法和頻率相關資訊。  您可以使用本文中的資訊來標識可用的不同解決方案，並瞭解不同監視解決方案的資料流程和連接要求。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>監視解決方案清單

下表列出了 Microsoft 提供的 Azure 中的[監視解決方案](solutions.md)。 欄中的項目表示解決方案會使用該方法將資料收集到 Azure 監視器中。  如果解決方案未選取任何欄，則它會從另一個 Azure 服務直接寫入至 Azure 監視器。 請遵循各自的連結來連到其詳細的文件，以取得詳細資訊。

各欄的說明如下所示：

- **微軟監視代理**- 在 Windows 和 Linux 上使用的代理從 SCOM 運行管理包，並從 Azure 監視解決方案。 在此組態中，代理程式會直接連線到 Azure 監視器，而不需連線至 Operations Manager 管理群組。 
- **Operations Manager** - 與 Microsoft Monitoring Agent 相同的代理程式。 在此組態中，它會[連線到 Operations Manager 管理群組](../platform/om-agents.md)，而該管理群組已連線到 Azure 監視器。 
-  **Azure 儲存體** - 解決方案會從 Azure 儲存體帳戶收集資料。 
- **是否需要 Operations Manager？** - 監視解決方案收集資料需要連接的操作管理器管理組。 
- **透過管理群組傳送的 Operations Manager 代理程式資料** - 如果代理程式[已連線到 SCOM 管理群組](../platform/om-agents.md)，則會從管理伺服器將資料傳送到 Azure 監視器。 在此情況下，代理程式不需要直接連線到 Azure 監視器。 如果未選取此方塊，即使代理程式已連線到 SCOM 管理群組，也會從代理程式直接將資料傳送到 Azure 監視器。 它必須能夠透過 [Azure 監視器閘道](../platform/gateway.md)與 Log Analytics 通訊。
- **收集頻率**- 指定監控解決方案收集資料的頻率。 



| **監視解決方案** | **平台** | **微軟監控代理** | **Operations Manager 代理程式** | **Azure 存儲** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [活動日誌分析](../platform/activity-log-collect.md) | Azure | | | | | | 與通知同時 |
| [AD 評估](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [AD 複寫狀態](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 天 |
| [代理程式健康狀態](solution-agenthealth.md) | Windows 與 Linux | &#8226; | &#8226; | | | &#8226; | 1 分鐘 |
| [警報管理](../platform/alert-management-solution.md)（納吉奧斯） |Linux |&#8226; | | | | |與抵達同時 |
| [警報管理](../platform/alert-management-solution.md)（紮比克斯） |Linux |&#8226; | | | | |1 分鐘 |
| [警報管理](../platform/alert-management-solution.md)（運營經理） |Windows | |&#8226; | |&#8226; |&#8226; |3 分鐘 |
| [Azure 網站恢復](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights Connector (已淘汰)](../platform/app-insights-connector.md) | Azure | | | |  |  | 與通知同時 |
| [自動化混合式背景工作角色](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Azure 應用程式閘道分析](azure-networking-analytics.md) | Azure |  |  |  |  |  | 與通知同時 |
| **監視解決方案** | **平台** | **微軟監控代理** | **Operations Manager 代理程式** | **Azure 存儲** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| [Azure 網路安全性群組分析 (已淘汰)](azure-networking-analytics.md) | Azure |  |  |  |  |  | 與通知同時 |
| [Azure SQL Analytics (預覽)](azure-sql.md) | Windows | | | | | | 1 分鐘 |
| [備份](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 與通知同時 |
| [容量與效能 (預覽)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |與抵達同時 |
| [更改跟蹤](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[不同](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [更改跟蹤](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[不同](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [容器](containers.md) | Windows 與 Linux | &#8226; | &#8226; |  |  |  | 3 分鐘 |
| [金鑰保存庫分析](azure-key-vault.md) |Windows | | | | | |與通知同時 |
| [惡意程式碼評估](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |每小時 |
| [網路效能監視器](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP 會每 5 秒交握一次，而資料會每 3 分鐘傳送一次 |
| [Office 365 分析 (預覽)](solution-office-365.md) |Windows | | | | | |與通知同時 |
| **監視解決方案** | **平台** | **微軟監控代理** | **Operations Manager 代理程式** | **Azure 存儲** | **是否需要 Operations Manager？** | **透過管理群組傳送的 Operations Manager 代理程式資料** | **收集頻率** |
| [Service Fabric 分析](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 分鐘 |
| [服務地圖](service-map.md) | Windows 與 Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL 評估](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 天 |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |與抵達同時 |
| [System Center Operations Manager 評定 (預覽)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 天 |
| [更新管理](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |安裝更新之後 15 分鐘，至少一天 2 次 |
| [更新整備小幫手](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 天 |
| [VMware 監控 (已淘汰)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 分鐘 |
| [Wire Data 2.0 (預覽)](wire-data.md) |Windows (2012 R2/8.1 或更新版本) |&#8226; |&#8226; | | | | 1 分鐘 |




## <a name="next-steps"></a>後續步驟
* 瞭解如何[安裝和使用監視解決方案](solutions.md)。
* 了解如何[建立查詢](../log-query/log-query-overview.md)，以分析監視解決方案所收集的資料。
