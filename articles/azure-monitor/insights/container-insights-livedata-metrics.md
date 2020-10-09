---
title: 使用容器的 Azure 監視器即時查看計量 |Microsoft Docs
description: 本文說明如何在不使用 kubectl 搭配容器的 Azure 監視器的情況下即時查看計量。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 81d7210778fd6b5d75fb4b4fa8e066d2e015174f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85338026"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何即時查看度量

適用于容器即時資料 (預覽) 功能的 Azure 監視器，可讓您即時視覺化叢集中節點和 pod 狀態的相關度量。 它會模擬、和命令的直接存取， `kubectl top nodes` `kubectl get pods –all-namespaces` `kubectl get nodes` 以呼叫、剖析和視覺化此深入解析所含之效能圖表中的資料。

本文提供詳細的總覽，並協助您瞭解如何使用這項功能。

>[!NOTE]
>這項功能不支援啟用為 [私人](https://azure.microsoft.com/updates/aks-private-cluster/) 叢集的 AKS 叢集。 這項功能依賴從瀏覽器透過 proxy 伺服器直接存取 Kubernetes API。 啟用網路安全性以封鎖此 proxy 的 Kubernetes API 將會封鎖此流量。

如需有關設定或疑難排解即時資料 (預覽) 功能的說明，請參閱我們的 [設定指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>運作方式

即時資料 (預覽) 功能可直接存取 Kubernetes API，您可以在 [這裡](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有關驗證模型的其他資訊。

這項功能會針對計量端點執行輪詢作業 (包括 `/api/v1/nodes` 、 `/apis/metrics.k8s.io/v1beta1/nodes` 和 `/api/v1/pods`) ，預設為每隔五秒。 這項資料會在您的瀏覽器中快取，並 **在 [叢集] 索引** 標籤上的容器 Azure 監視器中包含的四個效能圖表中，選取 [ **上線 (預覽]) **。 每個後續輪詢都會繪製到滾動五分鐘的視覺效果視窗中。

![叢集視圖中的 [上線] 選項](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

輪詢間隔是從 [ **設定間隔** ] 下拉式清單中設定，可讓您設定每1、5、15和30秒輪詢一次新資料。

![Go 即時下拉式輪詢間隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>建議您將輪詢間隔設定為一秒，並在一小段時間內針對問題進行疑難排解。 這些要求可能會影響叢集上 Kubernetes API 的可用性和節流。 之後，請重新設定為較長的輪詢間隔。

>[!IMPORTANT]
>這項功能在作業期間不會永久儲存任何資料。 當您關閉瀏覽器或離開功能時，會立即刪除在此會話期間所捕獲的所有資訊。 只有在五分鐘的時間範圍內，視覺效果才會有資料存在;任何超過五分鐘的計量也會永久刪除。

這些圖表無法釘選到您以即時模式查看的最後一個 Azure 儀表板。

## <a name="metrics-captured"></a>擷取的度量

### <a name="node-cpu-utilization---node-memory-utilization-"></a>節點 CPU 使用率%/節點記憶體使用率%

這兩個效能圖表對應于叫用 `kubectl top nodes` 和將 **CPU%** 和 **MEMORY%** 資料行的結果，對應至各自的圖表。

![Kubectl 上層節點範例結果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![節點 CPU 使用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![節點記憶體使用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分位數計算將在較大型的叢集中運作，以協助識別叢集中的極端節點。 例如，若要瞭解節點是否過度使用，以縮小用途。 利用 **最小** 匯總，您可以查看叢集中的使用率低的節點。 若要進一步調查，請選取 [ **節點** ] 索引標籤，並依 CPU 或記憶體使用量排序方格。

這也可協助您瞭解要將哪些節點推送至其限制，以及是否可能需要相應放大。 使用 **Max** 和 **P95** 匯總可協助您查看叢集中是否有節點的資源使用率很高。 若要進一步調查，您可以再次切換至 [ **節點** ] 索引標籤。

### <a name="node-count"></a>節點計數

此效能圖表對應至叫 `kubectl get nodes` 用，並將 [ **狀態** ] 資料行對應到依狀態類型分組的圖表。

![Kubectl 取得節點範例結果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![節點計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

節點會回報為 **就緒** 或 **未就緒** 狀態。 系統會計算 (，並) 建立總計數，而且這兩個匯總的結果會繪製成圖表。
例如，瞭解您的節點是否進入失敗狀態。 利用「 **未就緒** 」匯總，您可以快速查看叢集中目前處於「 **未就緒** 」狀態的節點數目。

### <a name="active-pod-count"></a>使用中的 pod 計數

此效能圖表對應至叫用的對等專案 `kubectl get pods –all-namespaces` ，並將圖表的 **status** 資料行對應到依狀態類型分組的資料行。

![Kubectl 取得 pod 範例結果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![節點 pod 計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>所解釋的狀態名稱 `kubectl` 在圖表中可能不會完全相符。

## <a name="next-steps"></a>後續步驟

查看 [記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data) 以查看預先定義的查詢和範例，以建立警示、視覺效果，或進一步分析您的叢集。
