---
title: 使用 Azure 監視器即時查看容器指標 |微軟文檔
description: 本文介紹了指標的即時視圖，而不將庫布ectl與 Azure 監視器一起使用容器。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216596"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何即時查看指標

Azure 監視器用於容器即時資料（預覽）功能，允許您即時視覺化有關群集中節點和 Pod 狀態的指標。 它類比直接存取`kubectl top nodes`，`kubectl get pods –all-namespaces`和`kubectl get nodes`命令來調用、 分析和視覺化此 Insight 中包含的性能圖表中的資料。 

本文提供了詳細的概述，並説明您瞭解如何使用此功能。  

>[!NOTE]
>此功能不支援作為[專用群集](https://azure.microsoft.com/updates/aks-private-cluster/)啟用的 AKS 群集。 此功能依賴于通過瀏覽器通過代理伺服器直接存取 Kubernetes API。 啟用網路安全性來阻止 Kubernetes API 來自此代理將阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 區域（包括 Azure 中國）都可用。 它當前不在 Azure 美國政府中可用。

有關設置或排除即時資料（預覽）功能或故障排除的説明，請查看我們的[設置指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>運作方式 

即時資料（預覽）功能直接存取 Kubernetes API，有關身份驗證模型的其他資訊可[在此處](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

此功能對指標終結點（包括`/api/v1/nodes`，`/apis/metrics.k8s.io/v1beta1/nodes`和`/api/v1/pods`） 執行輪詢操作，預設情況下每五秒執行一次輪詢操作。 此資料緩存在瀏覽器中，並通過選擇 **"Go Live"（預覽），** 在 **"群集**"選項卡上的容器 Azure 監視器中包含的四個性能圖表中繪製圖表。 每個後續輪詢都繪製成滾動的五分鐘視覺化視窗。 

![在群集視圖中上線選項](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

輪詢間隔從 **"設置"間隔**下拉清單進行配置，允許您每 1、5、15 和 30 秒設置新資料的輪詢。 

![轉到即時下拉輪詢間隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>我們建議將輪詢間隔設置為一秒，同時對問題進行短期故障排除。 這些請求可能會影響群集上的 Kubernetes API 的可用性和限制。 之後，重新配置為更長的輪詢間隔。 

>[!IMPORTANT]
>在此功能操作期間，不會永久存儲任何資料。 當您關閉瀏覽器或導航遠離功能時，將立即刪除此會話期間捕獲的所有資訊。 資料僅保留在五分鐘視窗內進行視覺化;任何超過五分鐘的指標也會永久刪除。

無法將這些圖表固定到您在即時模式下查看的最後一個 Azure 儀表板。

## <a name="metrics-captured"></a>擷取的度量

### <a name="node-cpu-utilization---node-memory-utilization-"></a>節點 CPU 利用率 % / 節點記憶體利用率 % 

這兩個性能圖表映射到等效于調用`kubectl top nodes`和捕獲 CPU% 和**MEMORY%** 列**MEMORY%** 的結果到相應的圖表。 

![庫布克特爾頂部節點示例結果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![節點 CPU 利用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![節點記憶體利用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分位數計算將在較大的群集中起作用，以説明識別群集中的異常值節點。 例如，瞭解節點是否未用於縮減用途。 利用**最小**聚合，您可以看到群集中的哪些節點利用率較低。 要進一步調查，請選擇 **"節點"** 選項卡，然後按 CPU 或記憶體利用率對網格進行排序。

這還可以説明您瞭解哪些節點被推送到其限制，以及是否需要橫向擴展。 利用**Max**和**P95**聚合可以説明您瞭解群集中是否有具有高資源利用率的節點。 為了進一步調查，您將再次切換到 **"節點"** 選項卡。

### <a name="node-count"></a>節點計數

此性能圖表映射到等效于調用`kubectl get nodes` **STATUS**列並將其映射到按狀態類型分組的圖表。

![庫布克特爾獲取節點示例結果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![節點計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

節點以 **"就緒"** 或 **"未就緒**"狀態報表。 它們被計數（並創建總計數），並繪製這兩個聚合的結果。
例如，瞭解節點是否處於失敗狀態。 利用 **"未就緒"** 聚合，您可以快速查看群集中當前處於 **"未就緒"** 狀態的節點數。

### <a name="active-pod-count"></a>活動窗格計數

此性能圖表映射到等效于調用`kubectl get pods –all-namespaces`和映射按狀態類型分組的"**狀態**"列。

![庫布克特爾獲取窗格示例結果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![節點窗格計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>由 其`kubectl`解釋的狀態名稱在圖表中可能不完全符合。 

## <a name="next-steps"></a>後續步驟

查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以創建警報、視覺化或對群集執行進一步分析。
