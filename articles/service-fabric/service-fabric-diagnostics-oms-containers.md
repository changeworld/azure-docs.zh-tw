---
title: 使用 Azure 監視器記錄監視容器
description: 使用 Azure 監視器記錄來監視在 Azure Service Fabric 叢集上執行的容器。
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.openlocfilehash: 8d4231de13da3f8b2960bd4852136f803a97a546
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614429"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>使用 Azure 監視器記錄監視容器
 
本文涵蓋設定 Azure 監視器記錄容器監視解決方案以查看容器事件所需的步驟。 若要設定您的叢集來收集容器事件，請參閱此[逐步教學課程](service-fabric-tutorial-monitoring-wincontainers.md)。 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>設定容器監視解決方案

> [!NOTE]
> 您需要為您的叢集設定 Azure 監視器記錄，並在您的節點上部署 Log Analytics 代理程式。 如果您沒有這麼做，請依照[設定 Azure 監視器記錄](service-fabric-diagnostics-oms-setup.md)中的步驟，並先[將 Log Analytics 代理程式新增至](service-fabric-diagnostics-oms-agent.md)叢集。

1. 使用 Azure 監視器記錄和 Log Analytics 代理程式設定叢集之後，請部署您的容器。 移至下一步之前，請等待容器部署完成。

2. 在 Azure Marketplace 中，搜尋 [容器監視解決方案]**，然後按一下顯示在 [監視 + 管理] 類別下的 [容器監視解決方案]**** 資源。

    ![新增容器解決方案](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. 將解決方案建立在已經為叢集建立的相同工作區內。 此變更會自動觸發代理程式，以開始收集容器上的 Docker 資料。 大約 15 分鐘左右，您應該會看到帶有傳入記錄和統計資料的解決方案亮起，如以下圖像所示。

    ![基本 Log Analytics 儀表板](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

代理程式可讓您收集數個可在 Azure 監視器記錄中查詢，或用來視覺化效能指標的容器特定記錄。 所收集的記錄類型有：

* ContainerInventory︰顯示容器位置、名稱和映像的相關資訊
* ContainerImageInventory︰已部署映像相關資訊，包括 ID 或大小
* ContainerLog︰特定的錯誤記錄、 docker 記錄 (stdout 等) 及其他項目
* ContainerServiceLog：已執行的 docker 精露命令
* 效能︰包括容器 CPU、記憶體、網路流量、磁碟 I/O，以及主機電腦的自訂計量



## <a name="next-steps"></a>後續步驟
* 深入瞭解[Azure 監視器記錄容器解決方案](../azure-monitor/insights/containers.md)。
* 深入了解 Service Fabric 上的容器協調流程 - [Service Fabric 與容器](service-fabric-containers-overview.md)
* 使用 Azure 監視器記錄中提供的[記錄搜尋和查詢](../log-analytics/log-analytics-log-searches.md)功能取得熟悉
* 設定 Azure 監視器記錄以設定[自動化警示](../log-analytics/log-analytics-alerts.md)規則，以協助偵測和診斷