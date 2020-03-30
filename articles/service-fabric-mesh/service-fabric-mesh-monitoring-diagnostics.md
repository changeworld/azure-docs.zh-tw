---
title: Azure 服務結構網格應用中的監視和診斷
description: 了解如何在 Azure 上監視和診斷 Service Fabric Mesh 應用程式。
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498157"
---
# <a name="monitoring-and-diagnostics"></a>監視和診斷
Azure Service Fabric Mesh 是一個受到完整管理的服務，讓開發人員能夠部署微服務應用程式，而不需管理虛擬機器、儲存體或網路功能。 Service Fabric Mesh 的監視和診斷可歸類為三種主要診斷資料類型：

- 應用程式記錄 - 根據您檢測應用程式的方式，這些會定義為來自您的容器化應用程式的記錄 (例如 Docker 記錄)
- 平台事件 - 來自 Mesh 平台並與您的容器作業相關的事件，目前包括容器啟用、停用及終止。
- 容器計量 - 您的容器適用的資源使用率和效能計量 (Docker 統計資料)

本文討論最新預覽版本可用的監視和診斷選項。

## <a name="application-logs"></a>應用程式記錄

您可以從您已部署的容器，依照容器檢視 Docker 記錄。 在 Service Fabric Mesh 應用程式模型中，每個容器都是您應用程式中的程式碼封裝。 若要查看相關聯的記錄與程式碼封裝，請使用下列命令：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> 您可以使用 "az mesh service-replica" 命令來取得複本名稱。 副本名稱從 0 遞增整數。

從投票應用程式的 VotingWeb.Code 容器查看記錄時，其外觀如下所示：

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>容器指標 

網格環境公開了一些指標，指示容器的性能。 以下指標可通過 Azure 門戶和 Azure 監視器 CLI 獲得：

| 計量 | 描述 | 單位|
|----|----|----|
| Cpu 利用 | 實際 Cpu/分配 Cpu 以百分比表示 | % |
| 記憶體利用 | 實際 Mem/已分配 Mem 的百分比 | % |
| 已分配 Cpu | 根據 Azure 資源管理器範本分配的 Cpu | 米利科茨 |
| 已分配記憶體 | 根據 Azure 資源管理器範本分配的記憶體 | MB |
| 實際Cpu | CPU 使用率 | 米利科茨 |
| 實際記憶體 | 記憶體使用量 | MB |
| 容器狀態 | 0 - 無效：容器狀態未知 <br> 1 - 掛起：容器已計畫啟動 <br> 2 - 啟動：容器正在啟動中 <br> 3 - 已啟動：容器已成功啟動 <br> 4 - 停止：容器正在停止 <br> 5 - 已停止：容器已成功停止 | N/A |
| 應用程式狀態 | 0 - 未知：狀態不可檢索 <br> 1 - 就緒：應用程式已成功運行 <br> 2 - 升級：正在進行升級 <br> 3 - 創建：正在創建應用程式 <br> 4 - 刪除：正在刪除應用程式 <br> 5 - 失敗：應用程式無法部署 | N/A |
| ServiceStatus | 0 - 無效：該服務當前沒有運行狀況 <br> 1 - 確定：服務正常  <br> 2 - 警告：可能有問題需要調查 <br> 3 - 錯誤：有一些問題需要調查 <br> 4 - 未知：狀態不可檢索 | N/A |
| 服務複製狀態 | 0 - 無效：副本當前沒有運行狀況 <br> 1 - 確定：服務正常  <br> 2 - 警告：可能有問題需要調查 <br> 3 - 錯誤：有一些問題需要調查 <br> 4 - 未知：狀態不可檢索 | N/A | 
| 重新開機計數 | 重新開機的容器數 | N/A |

> [!NOTE]
> 服務狀態和服務副本狀態值與服務結構中的[運行狀況狀態](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet)相同。 

每個指標都在不同的維度上可用，因此您可以看到不同級別的聚合。 當前維度清單如下：

* ApplicationName
* ServiceName
* 服務副本名稱
* 代碼包名稱

> [!NOTE]
> 代碼包名稱維度不適用於 Linux 應用程式。 

每個維度對應于[服務結構應用程式模型](service-fabric-mesh-service-fabric-resources.md#applications-and-services)的不同元件

### <a name="azure-monitor-cli"></a>Azure 監視器 CLI

[Azure 監視器 CLI 文檔中](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)提供了完整的命令清單，但我們在下面提供了一些有用的示例 

在每個示例中，資源識別碼 遵循此模式

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* 應用程式中容器的 CPU 利用率

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* 每個服務副本的記憶體利用率
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* 在 1 小時視窗中重新開機每個容器 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* 在 1 小時視窗中，名為"投票 Web"的服務的平均 CPU 利用率
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>計量瀏覽器

指標資源管理器是門戶中的邊欄選項卡，您可以在其中視覺化網格應用程式的所有指標。 此邊欄選項卡可在門戶中的應用程式頁面和 Azure 監視器邊欄選項卡中訪問，後者可用於查看支援 Azure 監視器的所有 Azure 資源的指標。 

![計量瀏覽器](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>後續步驟
* 若要深入了解 Service Fabric Mesh，請閱讀 [Service Fabric Mesh 概觀](service-fabric-mesh-overview.md)。
* 要瞭解有關 Azure 監視器指標命令的更多詳細資訊，請查看[Azure 監視器 CLI 文檔](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)。
