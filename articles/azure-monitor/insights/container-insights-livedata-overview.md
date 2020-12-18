---
title: 使用容器的 Azure 監視器來觀看即時資料 (預覽) |Microsoft Docs
description: 本文說明 Kubernetes 記錄、事件和 pod 計量的即時觀點，而不需要在容器的 Azure 監視器中使用 kubectl。
ms.topic: conceptual
ms.date: 12/17/2020
ms.custom: references_regions
ms.openlocfilehash: 3655ff8e5879aa4113753b5529c1e484fb079401
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672863"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何即時查看 Kubernetes 記錄、事件和 pod 計量

容器的 Azure 監視器包括即時資料 (預覽) 功能，這是一項先進的診斷功能，可讓您直接存取 Azure Kubernetes Service () stdout/stderror (、事件和 pod 計量) AKS 容器記錄。 它會公開 `kubectl logs -c` 、 `kubectl get` 事件和的直接存取 `kubectl top pods` 。 主控台窗格會顯示容器引擎所產生的記錄、事件和計量，以進一步協助即時疑難排解問題。

本文提供詳細的總覽，並協助您瞭解如何使用這項功能。

如需有關設定或疑難排解即時資料 (預覽) 功能的說明，請參閱我們的 [設定指南](container-insights-livedata-setup.md)。 這項功能可直接存取 Kubernetes API，您可以在 [這裡](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有關驗證模型的其他資訊。

## <a name="view-deployment-live-logs-preview"></a>查看部署即時記錄 (預覽) 
使用下列程式來查看屬於 AKS 叢集的部署的即時記錄，這些部署不受容器 Azure 監視器監視。 如果您的叢集使用 Azure 監視器的容器，請使用下列程式來查看節點、控制器、容器和部署的即時資料。

1. 在 Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在功能表的 [ **Kubernetes resources** ] 區段中，選取 [**工作負載**]。

3. 從 [ **部署** ] 索引標籤中選取部署。

4. 從部署的功能表中選取 [ **即時記錄] (預覽)** 。

5. 選取 pod 以開始收集即時資料。

    [![部署即時記錄](./media/container-insights-livedata-overview/live-data-deployment.png)](./media/container-insights-livedata-overview/live-data-deployment.png#lightbox)

## <a name="view-logs"></a>檢視記錄

您可以從 **節點**、 **控制器** 和 **容器** 視圖，查看容器引擎所產生的即時記錄資料。 若要查看記錄資料，請執行下列步驟。

1. 在 Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板上，于左側的 [ **監視** ] 底下，選擇 [ **見解**]。

3. 選取 [ **節點**]、[ **控制器**] 或 [ **容器** ] 索引標籤。

4. 從 [效能] 方格中選取物件，然後在右側的 [屬性] 窗格中，選取 [ **View live data (preview])** 選項。 如果 AKS 叢集是使用 Azure AD 設定單一登入，則系統會在瀏覽器會話期間第一次使用時提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。

    >[!NOTE]
    >當您從 [屬性] 窗格中選取 [ **分析中的視圖** ] 選項來從 log Analytics 工作區中查看資料時，記錄搜尋結果可能會顯示 **節點**、 **Daemon 集合**、 **複本集**、 **作業**、 **Cron 作業**、 **pod，以及** 可能不存在的 **容器** 。 如果無法在中使用的容器搜尋記錄， `kubectl` 也會在這裡失敗。 若要深入瞭解如何查看歷程記錄、事件和計量，請參閱「 [分析](container-insights-log-search.md#search-logs-to-analyze-data) 」功能。

成功驗證之後，[即時資料 (預覽]) 主控台窗格將會出現在 [效能資料] 方格下方，您可以在其中查看連續串流中的記錄資料。 如果提取狀態指示器顯示綠色的核取記號（位於窗格最右側），表示可以取出資料，並開始串流至您的主控台。

![節點屬性窗格視圖資料選項](./media/container-insights-livedata-overview/node-properties-pane.png)

窗格標題會顯示容器所分組的 pod 名稱。

## <a name="view-events"></a>檢視事件

當選取容器、pod、節點、ReplicaSet、DaemonSet、作業、CronJob 或部署時，當容器引擎從 **節點**、 **控制器**、 **容器** 和部署產生的即時事件資料時，您可以看到這些資料， **(預覽版)** view。 若要查看事件，請執行下列步驟。

1. 在 Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板上，于左側的 [ **監視** ] 底下，選擇 [ **見解**]。

3. 選取 [ **節點**]、[ **控制器**]、[ **容器**] 或 [ **部署 (預覽])** 索引標籤。

4. 從 [效能] 方格中選取物件，然後在右側的 [屬性] 窗格中，選取 [ **View live data (preview])** 選項。 如果 AKS 叢集是使用 Azure AD 設定單一登入，則系統會在瀏覽器會話期間第一次使用時提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。

    >[!NOTE]
    >當您從 [屬性] 窗格中選取 [ **分析中的視圖** ] 選項來從 log Analytics 工作區中查看資料時，記錄搜尋結果可能會顯示 **節點**、 **Daemon 集合**、 **複本集**、 **作業**、 **Cron 作業**、 **pod，以及** 可能不存在的 **容器** 。 如果無法在中使用的容器搜尋記錄， `kubectl` 也會在這裡失敗。 若要深入瞭解如何查看歷程記錄、事件和計量，請參閱「 [分析](container-insights-log-search.md#search-logs-to-analyze-data) 」功能。

成功驗證之後，[即時資料 (預覽]) 主控台窗格將會出現在 [效能資料] 方格下方。 如果提取狀態指示器顯示綠色的核取記號（位於窗格最右側），表示可以取出資料，並開始串流至您的主控台。

如果您選取的物件是容器，請選取窗格中的 [ **事件** ] 選項。 如果您選取節點、Pod 或控制器，則會自動選取 [查看事件]。

![控制器屬性窗格視圖事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)

窗格標題會顯示容器所分組的 Pod 名稱。

### <a name="filter-events"></a>篩選事件

在查看事件時，您可以使用 [搜尋] 列右邊的 [ **篩選** ] 框，進一步限制結果。 視您選取的資源而定，框會列出要從中選擇的 Pod、命名空間或叢集。

## <a name="view-metrics"></a>檢視計量

您可以根據容器引擎從 **節點** 或 **控制器** 視圖產生的即時計量資料，只在選取 **Pod** 時才能看到這些資料。 若要查看度量，請執行下列步驟。

1. 在 Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板上，于左側的 [ **監視** ] 底下，選擇 [ **見解**]。

3. 選取 [ **節點** ] 或 [ **控制器** ] 索引標籤。

4. 從效能方格中選取 **Pod** 物件，然後在右側的 [屬性] 窗格中，選取 [ **View live data (preview])** 選項。 如果 AKS 叢集是使用 Azure AD 設定單一登入，則系統會在瀏覽器會話期間第一次使用時提示您進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。

    >[!NOTE]
    >當您從 [屬性] 窗格中選取 [ **分析中的視圖** ] 選項來從 log Analytics 工作區中查看資料時，記錄搜尋結果可能會顯示 **節點**、 **Daemon 集合**、 **複本集**、 **作業**、 **Cron 作業**、 **pod，以及** 可能不存在的 **容器** 。 如果無法在中使用的容器搜尋記錄， `kubectl` 也會在這裡失敗。 若要深入瞭解如何查看歷程記錄、事件和計量，請參閱「 [分析](container-insights-log-search.md#search-logs-to-analyze-data) 」功能。

成功驗證之後，[即時資料 (預覽]) 主控台窗格將會出現在 [效能資料] 方格下方。 系統會抓取度量資料，並開始串流至您的主控台，以在兩個圖表中呈現。 窗格標題會顯示容器所分組的 pod 名稱。

![查看 Pod 計量範例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>使用即時資料檢視
下列各節說明您可以在不同的即時資料檢視中使用的功能。

### <a name="search"></a>搜尋
即時資料 (預覽) 功能包含搜尋功能。 在 [ **搜尋** ] 欄位中，您可以藉由輸入關鍵字或詞彙來篩選結果，並且反白顯示任何相符的結果，以允許快速審核。 在查看事件時，您可以使用 [搜尋] 列右邊的 [ **篩選** ] 框，進一步限制結果。 視您選取的資源而定，框會列出要從中選擇的 Pod、命名空間或叢集。

![即時資料主控台窗格篩選範例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

![適用于部署的即時資料主控台窗格篩選範例](./media/container-insights-livedata-overview/live-data-deployment-search.png)

### <a name="scroll-lock-and-pause"></a>滾動鎖和暫停

若要暫停 autoscroll 並控制窗格的行為，讓您可以手動方式在讀取的新資料中進行滾動，您可以使用 **scroll** 選項。 若要重新啟用 autoscroll，只要選取 [ **滾動** ] 選項即可。 您也可以選取 [ **暫停** ] 選項來暫停記錄檔或事件資料的抓取，然後當您準備好繼續時，只要選取 [ **播放**] 即可。

![即時資料主控台窗格暫停即時視圖](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

![即時資料主控台窗格暫停部署的即時查看](./media/container-insights-livedata-overview/live-data-deployment-pause.png)



>[!IMPORTANT]
>我們建議您只在針對問題進行疑難排解時，暫停或暫停 autoscroll 一小段時間。 這些要求可能會影響叢集上 Kubernetes API 的可用性和節流。

>[!IMPORTANT]
>這項功能在作業期間不會永久儲存任何資料。 當您關閉瀏覽器或離開瀏覽器時，會刪除會話期間所捕獲的所有資訊。 資料只會在度量功能的五分鐘範圍內針對視覺效果保持存在;超過五分鐘的任何計量也會一併刪除。 即時資料 (預覽) 緩衝區在合理的記憶體使用量限制內進行查詢。

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看 [記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data) 以查看預先定義的查詢和範例，以建立警示、視覺效果，或進一步分析您的叢集。
