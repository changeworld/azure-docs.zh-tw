---
title: 使用 Azure 監視器日誌監視容器
description: 使用 Azure 監視器日誌監視在 Azure 服務結構群集上運行的容器。
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614429"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>使用 Azure 監視器日誌監視容器
 
本文介紹設置 Azure 監視器日誌容器監視解決方案以查看容器事件所需的步驟。 若要設定您的叢集來收集容器事件，請參閱此[逐步教學課程](service-fabric-tutorial-monitoring-wincontainers.md)。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>設定容器監視解決方案

> [!NOTE]
> 您需要為群集設置 Azure 監視器日誌，並需要在節點上部署日誌分析代理。 如果沒有，請按照[設置 Azure 監視器日誌](service-fabric-diagnostics-oms-setup.md)中的步驟操作，然後首先[將日誌分析代理添加到群集](service-fabric-diagnostics-oms-agent.md)。

1. 使用 Azure 監視器日誌和日誌分析代理設置群集後，部署容器。 移至下一步之前，請等待容器部署完成。

2. 在 Azure Marketplace 中，搜尋 [容器監視解決方案]**，然後按一下顯示在 [監視 + 管理] 類別下的 [容器監視解決方案]**** 資源。

    ![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 將解決方案建立在已經為叢集建立的相同工作區內。 此變更會自動觸發代理程式，以開始收集容器上的 Docker 資料。 大約 15 分鐘左右，您應該會看到帶有傳入記錄和統計資料的解決方案亮起，如以下圖像所示。

    ![基本 Log Analytics 儀表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理支援收集多個特定于容器的日誌，這些日誌可以在 Azure 監視器日誌中查詢，或用於視覺化性能指示器。 所收集的記錄類型有：

* ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
* ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
* ContainerLog︰特定的錯誤記錄、 docker 記錄 (stdout 等) 及其他項目
* ContainerServiceLog：已執行的 docker 精露命令
* 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量



## <a name="next-steps"></a>後續步驟
* 瞭解有關[Azure 監視器日誌容器解決方案](../azure-monitor/insights/containers.md)的更多資訊。
* 深入了解 Service Fabric 上的容器協調流程 - [Service Fabric 與容器](service-fabric-containers-overview.md)
* 熟悉 Azure 監視器日誌中提供的[日誌搜索和查詢](../log-analytics/log-analytics-log-searches.md)功能
* 配置 Azure 監視器日誌以設置[自動警報](../log-analytics/log-analytics-alerts.md)規則，以説明檢測和診斷