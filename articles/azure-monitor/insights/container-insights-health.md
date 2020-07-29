---
title: 使用適用於容器的 Azure 監視器來監視 Kubernetes 叢集健康情況 | Microsoft Docs
description: 本文說明如何使用適用於容器的 Azure 監視器，來檢視和分析 AKS 叢集和非 AKS 叢集的健康情況。
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649585"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>使用適用於容器的 Azure 監視器來了解 Kubernetes 叢集健康情況

在適用於容器的 Azure 監視器所支援的任何 Kubernetes 叢集上，適用於容器的 Azure 監視器會監視受控基礎結構元件及其中執行的所有節點，並報告這些項目的健康情況。 此體驗超越在[多叢集檢視](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上計算和回報的叢集健全狀態，您現在可以了解叢集中的一個或多個節點是否受資源限制，或是，節點或 Pod 無法使用，而影響叢集中根據所策劃計量來執行的應用程式。

>[!NOTE]
>「健康情況」功能將在 2020 年 6 月底轉換至個人預覽版。 如需詳細資訊，請參閱下列 [Azure 更新通知](https://azure.microsoft.com/updates/ci-health-limited-preview/)。
>

若要深入了解如何啟用適用於容器的 Azure 監視器，請參閱[將適用於容器的 Azure 監視器上線](container-insights-onboard.md)。

>[!NOTE]
>若要支援 AKS 引擎叢集，請確認其符合下列內容：
>- 是否使用最新版的 [HELM 用戶端](https://helm.sh/docs/using_helm/)。
>- 容器化代理程式版本是 microsoft/oms:ciprod11012019。 若要升級代理程式，請參閱[在 Kubernetes 叢集上升級代理程式](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent)。
>

## <a name="overview"></a>概觀

在適用於容器的 Azure 監視器中，「健康情況」(預覽) 功能會為 Kubernetes 叢集提供主動的健康情況監視，以協助您識別和診斷問題。 其讓您能夠檢視偵測到的重大問題。 評估叢集健康情況的監視器會在叢集中的容器化代理程式上執行，並將健康情況資料寫入 Log Analytics 工作區中的 **KubeHealth** 資料表。 

Kubernetes 叢集健康情況會以下列 Kubernetes 物件和抽象概念所組織的許多監視案例作為依據：

- Kubernetes 基礎結構 - 藉由評估 CPU 和記憶體使用率，以及 Pod 可用性，為叢集中所部署節點上執行的 Kubernetes API 伺服器、ReplicaSets 和 Daemonset 提供彙總資料

    ![Kubernetes 基礎結構的健康情況彙總檢視](./media/container-insights-health/health-view-kube-infra-01.png)

- 節點 - 藉由評估 CPU 和記憶體使用率，以及 Kubernetes 所報告的節點狀態，提供節點集區及每個集區中個別節點狀態的彙總資料。

    ![節點健康情況的彙總檢視](./media/container-insights-health/health-view-nodes-01.png)

目只支援提供虛擬 kubelet 的狀態。 虛擬 kublet 節點的 CPU 和記憶體使用量健全狀態會回報為**未知**，因為不會收到來自這些項目的信號。

所有監視器都會在 [健康情況階層] 窗格中顯示為階層式配置，其中代表 Kubernetes 物件或抽象概念的彙總監視 (也就是 Kubernetes 基礎結構或節點) 是最上層的監視，其反映結合所有相依子監視器的健康情況。 用來衍生健康情況的主要監視案例如下：

* 評估節點和容器的 CPU 使用率。
* 評估節點和容器的記憶體使用率。
* 計算 Kubernetes 所回報就緒狀態而得來的 Pod 和節點狀態。

下列是用來表示狀態的圖示：

|圖示|意義|  
|--------|-----------|  
|![綠色核取圖示表示狀況良好](./media/container-insights-health/healthyicon.png)|成功，健康情況正常 (綠色)|  
|![黃色三角形和驚嘆號表示警告](./media/container-insights-health/warningicon.png)|警告 (黃色)|  
|![具有白色 X 的紅色按鈕表示危急狀態](./media/container-insights-health/criticalicon.png)|危急 (紅色)|  
|![呈現灰色的圖示](./media/container-insights-health/grayicon.png)|未知 (灰色)|  

## <a name="monitor-configuration"></a>監視設定

若要了解適用於容器的 Azure 監視器的「健康情況」功能會使監視器具有哪些行為和設定，請參閱[健康情況監視設定指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>檢視 AKS 叢集或非 AKS 叢集的健康情況

若要在適用於容器的 Azure 監視器上存取「健康情況」(預覽) 功能，請從 Azure 入口網站的左側窗格中選取 [見解]。 在 [Insights] 區段下方，選取 [容器]。 

若要從非 AKS 叢集 (也就是裝載於內部部署或 Azure Stack 的 AKS 引擎叢集) 中檢視健康情況，請從 Azure 入口網站的左側窗格中選取 [Azure 監視器]。 在 [Insights] 區段下方，選取 [容器]。  在多叢集頁面上，從清單中選取非 AKS 叢集。

在適用於容器的 Azure 監視器中，從 [叢集] 頁面上選取 [健康情況]。

![叢集健康情況儀表板範例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>檢閱叢集健康情況

[健康情況] 頁面開啟時，[健康情況] 方格中會預設為選取 [Kubernetes 基礎結構]。  方格會摘要說明 Kubernetes 基礎結構和叢集節點目前的健康情況彙總。 選取任一健康情況時會更新 [健全情況階層] 窗格 (也就是中間窗格) 中的結果，也會顯示階層式配置中的所有子監視器，並顯示其目前的健全狀態。 若要檢視任何相依監視器的詳細資訊，您可以選取其中一個監視器，其 [屬性] 窗格將會自動顯示在頁面的右側。 

![叢集健康情況屬性窗格](./media/container-insights-health/health-view-property-pane.png)

在 [屬性] 窗格中，您會了解下列各項：

- 在 [概觀] 索引標籤上，其會顯示所選取監視器的目前狀態、上次計算監視器的時間，以及上次發生狀態變更的時間。 視階層中選取的監視器類型而定，可能還會顯示其他資訊。

    如果您在 [健康情況階層] 窗格中選取彙總監視器，在 [屬性] 窗格的 [概觀] 索引標籤底下，就會顯示階層中子監視器總數的彙總資料，以及有多少彙總監視器處於 [危急]、[警告] 和 [狀況良好] 的狀態。 

    ![彙總監視器中健康情況屬性窗格的 [概觀] 索引標籤](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果您在 [健康情況階層] 窗格中選取單元監視器，其也會在 [上次狀態變更] 底下顯示過去四小時內，容器化代理程式所計算和報告的先前樣本。 這會以單元監視器計算為基礎，在比較數個連續值後判斷其狀態。 例如，如果您選取 [Pod 就緒狀態] 單元監視器，其會顯示由參數 ConsecutiveSamplesForStateTransition 所控制的最後兩個樣本。 如需詳細資訊，請參閱[單元監視器](container-insights-health-monitors-config.md#unit-monitors)來查看詳細描述。
    
    ![健康情況屬性窗格的 [概觀] 索引標籤](./media/container-insights-health/health-overview-unit-monitor.png)

    如果 [上次狀態變更] 所回報的時間是一天或更早的日期，則表示監視器的狀態沒有任何變更。 不過，如果針對單元監視器收到的最後一個樣本超過四小時，這可能表示容器化代理程式尚未傳送資料。 如果代理程式知道特定資源存在 (例如節點)，但其尚未從節點的 CPU 或記憶體使用率監視器 (以此為例) 接收資料，則監視器的健全狀態會設定為**未知**。  

- 在 [Config] 索引標籤上，其會顯示預設的組態參數設定 (僅適用於單元監視器，而不是彙總監視器) 及其值。
- 在 [知識] 索引標籤上，其包含了說明監視器行為的資訊，以及其如何評估狀況不良的情況。

此頁面上的監視資料不會自動重新整理，您必須選取頁面頂端的 [重新整理]，才能查看到從叢集收到的最新健全狀態。

## <a name="next-steps"></a>後續步驟

檢視[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)來查看預先定義的查詢和範例，以進行評估或自訂來警示、視覺化或分析您的叢集。
