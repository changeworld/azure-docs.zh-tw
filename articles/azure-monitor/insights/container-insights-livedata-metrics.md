---
title: 使用容器的 Azure 監視器即時查看計量 |Microsoft Docs
description: 本文描述計量的即時觀點，而不使用 kubectl 與容器的 Azure 監視器。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79216596"
---
# <a name="how-to-view-metrics-in-real-time"></a>如何即時查看計量

[容器即時資料（預覽）] 功能的 Azure 監視器可讓您即時將叢集的相關計量視覺化到叢集中的節點和 pod 狀態。 它會模擬`kubectl top nodes`、 `kubectl get pods –all-namespaces`和`kubectl get nodes`命令的直接存取，以呼叫、剖析及視覺化此見解隨附之效能圖表中的資料。 

本文提供詳細的總覽，並協助您瞭解如何使用這項功能。  

>[!NOTE]
>此功能不支援已啟用為[私人](https://azure.microsoft.com/updates/aks-private-cluster/)叢集的 AKS 叢集。 此功能依賴從瀏覽器透過 proxy 伺服器直接存取 Kubernetes API。 啟用網路安全性以封鎖此 proxy 的 Kubernetes API 將會封鎖此流量。 

>[!NOTE]
>這項功能適用于所有 Azure 區域，包括 Azure 中國。 Azure 美國政府目前無法使用此功能。

如需設定或疑難排解「即時資料」（預覽）功能的說明，請參閱我們的[設定指南](container-insights-livedata-setup.md)。

## <a name="how-it-works"></a>運作方式 

即時資料（預覽）功能會直接存取 Kubernetes API，而有關驗證模型的其他資訊則可以在[這裡](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

這項功能會針對計量端點（包括`/api/v1/nodes`、 `/apis/metrics.k8s.io/v1beta1/nodes`和`/api/v1/pods`）執行輪詢作業，預設為每五秒一次。 這項資料會在瀏覽器中快取，並透過選取 [**上線（預覽）**]，在 [叢集] 索引標籤**上的容器**Azure 監視器中包含的四個效能圖表中繪製。 每個後續的輪詢都會繪製到滾動五分鐘的視覺效果視窗中。 

![叢集視圖中的上線選項](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

[輪詢間隔] 是從 [**設定間隔**] 下拉式設定，可讓您在每1、5、15和30秒輪詢新資料。 

![[上線] 下拉輪詢間隔](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>我們建議您將輪詢間隔設定為1秒，並在一小段時間內進行問題的疑難排解。 這些要求可能會影響叢集上 Kubernetes API 的可用性和節流。 之後，重新設定為較長的輪詢間隔。 

>[!IMPORTANT]
>在此功能的操作期間，不會永久儲存任何資料。 當您關閉瀏覽器或離開功能時，會立即刪除在此會話期間所捕捉到的所有資訊。 只有五分鐘時間範圍內的視覺效果才會顯示資料;超過五分鐘的任何計量也會永久刪除。

這些圖表無法釘選到您在 live 模式中觀看的最後一個 Azure 儀表板。

## <a name="metrics-captured"></a>擷取的度量

### <a name="node-cpu-utilization---node-memory-utilization-"></a>節點 CPU 使用率%/節點記憶體使用率% 

這兩個效能圖表會對應至對等`kubectl top nodes`的，叫用並將**CPU%** 和**記憶體%** 資料行的結果捕捉到各自的圖表。 

![Kubectl 前幾個節點範例結果](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![節點 CPU 使用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![節點記憶體使用率百分比圖表](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

百分位數計算會在較大的叢集內運作，以協助識別叢集中的極端節點。 例如，若要瞭解節點是否在使用中，以進行相應減少的目的。 利用**最小**匯總，您可以查看哪些節點在叢集中具有低使用率。 若要進一步調查，請選取 [**節點**] 索引標籤，然後依 CPU 或記憶體使用率排序方格。

這也可協助您瞭解哪些節點會被推送至其限制，以及是否需要相應放大。 同時利用**Max**和**P95**匯總可協助您查看叢集中是否有節點具有高資源使用率。 若要進一步調查，您會再次切換至 [**節點**] 索引標籤。

### <a name="node-count"></a>節點計數

此效能圖表會對應到叫用`kubectl get nodes` ，並將**status**資料行對應至依狀態類型分組的圖表。

![Kubectl 取得節點範例結果](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![節點計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

節點會回報為 [**就緒**] 或 [**未就緒**] 狀態。 系統會計算它們（並建立總計數），而這兩個匯總的結果會繪製在圖表上。
例如，若要瞭解您的節點是否落在失敗狀態。 利用 [**未就緒**] 匯總，您可以快速查看叢集中目前處於 [**未就緒**] 狀態的節點數目。

### <a name="active-pod-count"></a>使用中的 pod 計數

此效能圖表會對應到叫用的`kubectl get pods –all-namespaces`對等，並將 [**狀態**] 資料行對應到依狀態類型分組的圖表。

![Kubectl 取得 pod 範例結果](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![節點 pod 計數圖表](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>由`kubectl`所解讀的狀態名稱，可能不會完全符合圖表。 

## <a name="next-steps"></a>後續步驟

查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以建立警示、視覺效果，或執行進一步的叢集分析。
