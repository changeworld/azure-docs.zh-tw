---
title: 使用容器的 Azure 監視器監視 Kubernetes 叢集健全狀況 |Microsoft Docs
description: 本文說明如何使用適用于容器的 Azure 監視器，來查看和分析 AKS 和非 AKS 叢集的健康情況。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843985"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>了解使用適用於容器之 Azure 監視器的 Kubernetes 叢集健康狀態

使用容器的 Azure 監視器，它會監視並報告受管理基礎結構元件的健全狀況狀態，以及針對容器 Azure 監視器所支援的任何 Kubernetes 叢集上執行的所有節點。 此體驗延伸超過叢集健全狀況狀態，並會在[多叢集視圖](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上進行計算並回報，現在您可以瞭解叢集中的一或多個節點是否受資源限制，或節點或 pod 是否無法使用，而可能會影響叢集中的執行中應用程式（以策劃的計量為基礎）。

>[!NOTE]
>此健全狀況功能目前為公開預覽階段。
>

如需如何啟用容器 Azure 監視器的詳細資訊，請參閱將[容器上架 Azure 監視器](container-insights-onboard.md)。

>[!NOTE]
>若要支援 AKS Engine 叢集，請確認它符合下列內容：
>- 它使用最新版的[HELM 用戶端](https://helm.sh/docs/using_helm/)。
>- 容器化代理程式版本為*microsoft/oms： ciprod11012019*。 若要升級代理程式，請參閱[升級 Kubernetes 叢集上的代理程式](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)。
>

## <a name="overview"></a>概觀

在容器的 Azure 監視器中，[健康情況（預覽）] 功能會提供 Kubernetes 叢集的主動式健全狀況監視，以協助您識別和診斷問題。 它讓您能夠看到所偵測到的重大問題。 監視叢集上容器化代理程式上執行的叢集健全狀況，並將健康情況資料寫入 Log Analytics 工作區中的**KubeHealth**資料表。 

Kubernetes 叢集健康情況是根據下列 Kubernetes 物件和抽象概念所組織的許多監視案例：

- Kubernetes 基礎結構-藉由評估 CPU 和記憶體使用率，以及 pod 可用性，提供 Kubernetes API 伺服器、ReplicaSets 和 Daemonset 的匯總套件，在部署于叢集的節點上執行

    ![Kubernetes 基礎結構健全狀況匯總套件視圖](./media/container-insights-health/health-view-kube-infra-01.png)

- 節點-藉由評估 CPU 和記憶體使用率，以及 Kubernetes 所報告的節點狀態，提供節點集區的匯總以及每個集區中個別節點的狀態。

    ![節點健全狀況匯總視圖](./media/container-insights-health/health-view-nodes-01.png)

目前只支援虛擬 kubelet 的狀態。 虛擬 kublet 節點的 CPU 和記憶體使用量健全狀況狀態會回報為 [**未知**]，因為不會收到信號。

所有監視器都會在 [健全狀況階層] 窗格中顯示為階層式配置，其中代表 Kubernetes 物件或抽象的匯總監視（也就是 Kubernetes 基礎結構或節點）是最上層的監視，反映所有的結合健全狀況相依的子系監視。 用來衍生健全狀況的主要監視案例如下：

* 評估節點和容器的 CPU 使用率。
* 評估節點和容器的記憶體使用率。
* Pod 和節點的狀態，取決於 Kubernetes 回報的就緒狀態計算。

用來表示狀態的圖示如下所示：

|圖示|意義|  
|--------|-----------|  
|![綠色核取圖示表示狀況良好](./media/container-insights-health/healthyicon.png)|成功，健全狀況正常（綠色）|  
|![黃色三角形和驚嘆號為警告](./media/container-insights-health/warningicon.png)|警告（黃色）|  
|![具有白色 X 的紅色按鈕表示重大狀態](./media/container-insights-health/criticalicon.png)|重大（紅色）|  
|![呈現灰色圖示](./media/container-insights-health/grayicon.png)|未知（灰色）|  

## <a name="monitor-configuration"></a>監視設定

若要瞭解每個支援容器健全狀況功能 Azure 監視器的監視行為和設定，請參閱[健康情況監視器設定指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>查看 AKS 或非 AKS 叢集的健全狀況

從 Azure 入口網站的左窗格中選取 [**深入**解析]，即可直接從 AKS 叢集存取適用于容器的 Azure 監視器健康情況（預覽）功能。 在 [Insights] 區段下方，選取 [容器]。 

若要從非 AKS 叢集（也就是裝載于內部部署或 Azure Stack 的 AKS 引擎叢集）中查看健全狀況，請從 Azure 入口網站的左窗格中選取 [ **Azure 監視器**]。 在 [Insights] 區段下方，選取 [容器]。  在 [多叢集] 頁面上，從清單中選取非 AKS 叢集。

在**適用于容器的 Azure 監視器**中，從 叢集 頁面選取 **健康**情況。

![群集健全狀況儀表板範例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>檢查叢集健全狀況

當 [健康情況] 頁面開啟時，預設會在 [**健全狀況**] 方格中選取 [ **Kubernetes 基礎結構**]。  方格會摘要說明 Kubernetes 基礎結構和叢集節點的目前健全狀況匯總狀態。 選取任一健全狀況會更新 [健全狀況階層] 窗格中的結果（也就是中間窗格），並顯示階層式配置中的所有子監視器，顯示其目前的健全狀況狀態。 若要查看任何相依監視的詳細資訊，您可以選取其中一個，而 [屬性] 窗格會自動顯示在頁面的右側。 

![群集健全狀況屬性窗格](./media/container-insights-health/health-view-property-pane.png)

在 [屬性] 窗格中，您會瞭解下列各項：

- 在 [**總覽**] 索引標籤上，它會顯示所選取之監視器的目前狀態、上次計算監視的時間，以及上次發生狀態變更的時間。 視階層中選取的監視器類型而定，會顯示其他資訊。

    如果您在 [健全狀況階層] 窗格中選取匯總監視，在 [屬性] 窗格的 [**總覽**] 索引標籤底下，它會顯示階層中子監視總數的匯總，以及有多少部匯總監視器處於 [重大]、[警告] 和 [狀況良好] 狀態。 

    ![匯總監視的健全狀況屬性窗格 [總覽] 索引標籤](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果您在 [健康情況階層] 窗格中選取單位監視，它也會顯示在 [**上次狀態**] 下，[變更容器化代理程式在過去四小時內所計算和報告的先前範例]。 這是以單位監視計算為基礎，用來比較數個連續值來判斷其狀態。 例如，如果您選取 [ *Pod 就緒狀態*單位] 監視器，它會顯示由參數*ConsecutiveSamplesForStateTransition*所控制的最後兩個範例。 如需詳細資訊，請參閱[單位監視](container-insights-health-monitors-config.md#unit-monitors)的詳細描述。
    
    ![健全狀況屬性窗格 [總覽] 索引標籤](./media/container-insights-health/health-overview-unit-monitor.png)

    如果 [**上次狀態**] 所回報的時間變更為一天或更久，表示監視的狀態沒有任何變更。 不過，如果針對單位監視收到的最後一個樣本超過四小時，這可能表示容器化代理程式尚未傳送資料。 如果代理程式知道特定資源存在（例如節點），但尚未從節點的 CPU 或記憶體使用率監視器接收資料（例如），則監視的健全狀況狀態會設定為 [**未知**]。  

- **在 [設定**] 索引標籤上，它會顯示預設的設定參數設定（僅適用于單位監視，而不是匯總監視）及其值。
- 在 [**知識**] 索引標籤中，它包含說明監視行為的資訊，以及它如何評估狀況不良的狀況。

此頁面上的監視資料不會自動重新整理，您必須**選取頁面**頂端的 [重新整理]，以查看從叢集收到的最新健全狀況狀態。

## <a name="next-steps"></a>後續步驟

查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和評估或自訂的範例，以警示、視覺化或分析您的叢集。
