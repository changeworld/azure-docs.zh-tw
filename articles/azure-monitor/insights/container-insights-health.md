---
title: 使用 Azure 監視器監視容器庫伯內斯群集運行狀況 |微軟文檔
description: 本文介紹如何使用容器的 Azure 監視器查看和分析 AKS 和非 AKS 群集的運行狀況。
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843985"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>使用容器的 Azure 監視器瞭解庫伯內斯群集運行狀況

使用容器的 Azure 監視器，它監視和報告託管基礎結構元件和在 Azure 監視器支援的任何 Kubernetes 群集上運行的所有節點的運行狀況狀態。 此體驗超出了在[多群集視圖](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)上計算和報告的群集運行狀況狀態，現在您可以瞭解群集中的一個或多個節點是否受資源約束，或者節點或 pod 不可用，可能會影響基於精心策劃的指標的群集中的正在運行的應用程式。

>[!NOTE]
>此時，"運行狀況"功能處於公共預覽版中。
>

有關如何為容器啟用 Azure 監視器的資訊，請參閱[容器的板載 Azure 監視器](container-insights-onboard.md)。

>[!NOTE]
>要支援 AKS 引擎群集，請驗證它滿足以下內容：
>- 它使用最新版本的[HELM用戶端](https://helm.sh/docs/using_helm/)。
>- 容器化代理版本是*微軟/oms：ciprod11012019*。 要升級代理，請參閱[庫伯內斯群集上的升級代理](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster)。
>

## <a name="overview"></a>總覽

在容器的 Azure 監視器中，"運行狀況（預覽）"功能提供 Kubernetes 群集的主動運行狀況監視，以説明您識別和診斷問題。 它使您能夠查看檢測到的重要問題。 監視器評估群集中容器化代理上運行的群集運行狀況，並將運行狀況資料寫入日誌分析工作區中的**KubeHealth**表。 

庫伯內斯群集運行狀況基於以下 Kubernetes 物件和抽象組織的多個監視方案：

- Kubernetes 基礎結構 - 通過評估 CPU 和記憶體利用率以及 Pods 可用性，提供在群集中部署的節點上運行的 Kubernetes API 伺服器、複本集和守護程式集的匯總

    ![庫伯內斯基礎設施運行狀況匯總視圖](./media/container-insights-health/health-view-kube-infra-01.png)

- 節點 - 通過評估 CPU 和記憶體利用率以及 Kubernetes 報告節點的狀態，提供每個池中節點池和單個節點狀態的匯總。

    ![節點運行狀況匯總視圖](./media/container-insights-health/health-view-nodes-01.png)

目前，僅支援虛擬庫貝萊特的狀態。 虛擬庫布節點的 CPU 和記憶體利用率的運行狀況狀態報表為 **"未知"，** 因為未從它們接收信號。

所有監視器都顯示在"運行狀況層次結構"窗格中的分層佈局中，其中表示庫伯奈斯物件或抽象（即 Kubernetes 基礎結構或節點）的聚合監視器是反映所有運行狀況組合的監視器。依賴子監視器。 用於派生運行狀況的關鍵監視方案包括：

* 評估節點和容器的 CPU 利用率。
* 評估節點和容器的記憶體利用率。
* 基於 Kubernets 報告的就緒狀態的計算的 Pod 和節點的狀態。

用來指示狀態的圖示如下：

|圖示|意義|  
|--------|-----------|  
|![綠色核取圖示表示狀況良好](./media/container-insights-health/healthyicon.png)|成功，健全狀況正常 (綠色)|  
|![黃色三角形和驚嘆號表示警告](./media/container-insights-health/warningicon.png)|警告 (黃色)|  
|![具有白色 X 的紅色按鈕表示重大狀態](./media/container-insights-health/criticalicon.png)|重大 (紅色)|  
|![灰顯圖示](./media/container-insights-health/grayicon.png)|未知（灰色）|  

## <a name="monitor-configuration"></a>監視器配置

要瞭解支援容器運行狀況功能的每個監視器的行為和配置，請參閱[運行狀況監視器配置指南](container-insights-health-monitors-config.md)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com)。 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>查看 AKS 或非 AKS 群集的運行狀況

通過從 Azure 門戶中的左側窗格中選擇 **"見解"，** 可以直接從 AKS 群集訪問容器運行狀況（預覽）功能。 在 [Insights]**** 區段下方，選取 [容器]****。 

要查看非 AKS 群集（即本地或 Azure 堆疊上託管的 AKS 引擎群集）的運行狀況，請從 Azure 門戶中的左窗格中選擇**Azure 監視器**。 在 [Insights]**** 區段下方，選取 [容器]****。  在多群集頁上，從清單中選擇非 AKS 群集。

在容器的 Azure 監視器中，從 **"群集"** 頁中選擇 **"運行狀況**"。

![群集運行狀況儀表板示例](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>查看群集運行狀況

打開"運行狀況"頁時，預設情況下在 **"運行狀況"網格**中選擇**庫伯內特基礎結構**。  網格總結了庫貝內斯基礎結構和叢集節點的當前運行狀況匯總狀態。 選擇任一運行狀況方面會更新"運行狀況層次結構"窗格（即中間窗格）中的結果，並在分層佈局中顯示所有子監視器，顯示其當前運行狀況狀態。 要查看有關任何從屬監視器的詳細資訊，可以選擇一個屬性窗格，並自動顯示在頁面右側。 

![群集運行狀況屬性窗格](./media/container-insights-health/health-view-property-pane.png)

在屬性窗格中，您將瞭解以下內容：

- 在 **"概述"** 選項卡上，它顯示所選監視器的目前狀態、上次計算監視器的時以及上次狀態更改的次。 根據層次結構中選擇的監視器類型顯示其他資訊。

    如果在"運行狀況層次結構"窗格中選擇聚合監視器，則在屬性窗格的 **"概述"** 選項卡下，將顯示層次結構中子監視器總數以及處於嚴重、警告和狀態的聚合監視器數的匯總。 

    ![用於聚合監視器的運行狀況屬性窗格"概述"選項卡](./media/container-insights-health/health-overview-aggregate-monitor.png)

    如果在"運行狀況層次結構"窗格中選擇單位監視器，它還在 **"上次狀態"** 下顯示容器化代理在過去四小時內計算和報告以前的示例。 這基於用於比較多個連續值以確定其狀態的單位監視器計算。 例如，如果選擇了 Pod*就緒狀態*單元監視器，它將顯示由參數 *"連續採樣ForState轉換*"控制的最後兩個樣本。 有關詳細資訊，請參閱[單元監視器](container-insights-health-monitors-config.md#unit-monitors)的詳細說明。
    
    ![運行狀況屬性窗格"概述"選項卡](./media/container-insights-health/health-overview-unit-monitor.png)

    如果**Last 狀態更改**報告的時間為一天或更久，則它是監視器狀態沒有變化的結果。 但是，如果為單元監視器接收的最後一個樣本超過 4 小時，這可能表示容器化代理未發送資料。 如果代理知道存在特定資源（例如 Node），但它尚未從節點的 CPU 或記憶體利用率監視器接收資料（例如），則監視器的運行狀況狀態設置為 **"未知**"。  

- 在**Config**選項卡上，它顯示預設配置參數設置（僅適用于單元監視器，而不是聚合監視器）及其值。
- 在 **"知識"** 選項卡上，它包含解釋監視器行為及其評估不正常情況的方式的資訊。

監視此頁面上的資料不會自動刷新，您需要選擇頁面頂部的 **"刷新"** 以查看從群集接收的最新運行狀況狀態。

## <a name="next-steps"></a>後續步驟

查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以評估或自訂以提醒、視覺化或分析群集。
