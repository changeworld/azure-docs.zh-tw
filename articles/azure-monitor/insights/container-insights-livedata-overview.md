---
title: 使用適用于容器的 Azure 監視器來觀看即時資料（預覽） |Microsoft Docs
description: 本文說明 Kubernetes 記錄、事件和 pod 計量的即時觀點，而不在容器的 Azure 監視器中使用 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 38615acf079c33c49e27394bfdb9b8104e5a68ac
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872105"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何即時查看 Kubernetes 記錄、事件和 pod 計量

適用于容器的 Azure 監視器包含即時資料（預覽）功能，這是一項先進的診斷功能，可讓您直接存取您的 Azure Kubernetes Service （AKS）容器記錄（stdout/stderror）、事件和 pod 計量。 它會公開`kubectl logs -c`、 `kubectl get`事件和`kubectl top pods`的直接存取權。 主控台窗格會顯示容器引擎所產生的記錄、事件和計量，以進一步協助針對問題進行即時疑難排解。

本文提供詳細的總覽，並協助您瞭解如何使用這項功能。 

>[!NOTE]
>此功能不支援已啟用為[私人](https://azure.microsoft.com/updates/aks-private-cluster/)叢集的 AKS 叢集。 此功能依賴從瀏覽器透過 proxy 伺服器直接存取 Kubernetes API。 啟用網路安全性以封鎖此 proxy 的 Kubernetes API 將會封鎖此流量。 

>[!NOTE]
>這項功能適用于所有 Azure 區域，包括 Azure 中國。 Azure 美國政府目前無法使用此功能。

如需設定即時資料（預覽）功能或進行疑難排解的說明，請參閱我們的[設定指南](container-insights-livedata-setup.md)。 這項功能會直接存取 Kubernetes API，而您可以在[這裡](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到有關驗證模型的其他資訊。 

## <a name="live-data-preview-functionality-overview"></a>即時資料（預覽）功能總覽

### <a name="search"></a>搜尋

![即時資料主控台窗格篩選範例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

即時資料（預覽）功能包含搜尋功能。 在 [**搜尋**] 欄位中，您可以藉由輸入關鍵字或詞彙來篩選結果，並反白顯示任何相符的結果，以允許快速審查。 在查看事件時，您可以使用搜尋列右邊的 [**篩選**] 膠囊按鈕，進一步限制結果。 視您選取的資源而定，膠囊按鈕會列出要從中選擇的 Pod、命名空間或叢集。  

### <a name="scroll-lock-and-pause"></a>Scroll Lock 和 Pause 

若要暫停 autoscroll 並控制窗格的行為，可讓您手動流覽新的資料讀取，您可以使用**scroll**選項。 若要重新啟用 autoscroll，只要再次選取 [**滾動**條] 選項即可。 您也可以藉由選取 [**暫停**] 選項來暫停抓取記錄檔或事件資料，當您準備好繼續時，只要選取 [**播放**] 即可。  

![即時資料主控台窗格暫停 live view](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>建議您在針對問題進行疑難排解時，只暫停或暫停 autoscroll 一小段時間。 這些要求可能會影響叢集上 Kubernetes API 的可用性和節流。 

>[!IMPORTANT]
>在此功能的操作期間，不會永久儲存任何資料。 當您關閉瀏覽器或從中流覽時，會刪除會話期間所捕捉到的所有資訊。 只有在度量功能的五分鐘時間範圍內，視覺效果才會顯示資料;超過五分鐘的任何度量也會一併刪除。 即時資料（預覽）緩衝區會在合理的記憶體使用量限制內進行查詢。

## <a name="view-logs"></a>檢視記錄

您可以從 [**節點**]、[**控制器**] 和 [**容器**] 視圖，查看容器引擎產生的即時記錄資料。 若要查看記錄資料，請執行下列步驟。

1. 在 [Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板的左側 [**監視**] 底下，選擇 [**深入**解析]。 

3. 選取 [**節點**]、[**控制器**] 或 [**容器**] 索引標籤。

4. 從 [效能] 方格中選取物件，然後在右側找到的 [屬性] 窗格上，選取 [**查看即時資料（預覽）** ] 選項。 如果 AKS 叢集是使用 Azure AD 來設定單一登入，則系統會提示您在瀏覽器會話期間第一次使用時進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >從您的 Log Analytics 工作區中，選取 [屬性] 窗格的 [**在分析中查看**] 選項來觀看資料時，記錄搜尋結果可能會顯示**節點**、背景程式**集合**、**複本集**、**作業**、 **Cron 作業** **、pod，以及可能**已不存在的**容器**。 若嘗試搜尋無法在中`kubectl`使用的容器記錄，這裡也會失敗。 請參閱[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，以深入瞭解如何查看歷程記錄、事件和計量。  

成功驗證之後，[即時資料（預覽）] 主控台窗格會出現在 [效能資料] 方格下方，您可以在其中查看連續資料流程中的記錄資料。 如果提取狀態指示器顯示綠色核取記號（位於窗格最右邊），表示可以抓取資料，並開始串流至您的主控台。  

![節點屬性窗格視圖資料選項](./media/container-insights-livedata-overview/node-properties-pane.png)  

窗格標題會顯示容器所群組的 pod 名稱。

## <a name="view-events"></a>檢視事件

當容器、pod、節點、ReplicaSet、DaemonSet、作業、CronJob 或部署已選取時，您可以從 [**節點**]、[**控制器**]、[**容器**] 和 [**部署（預覽）** ] 視圖的容器引擎產生即時事件資料。 若要查看事件，請執行下列步驟。

1. 在 [Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板的左側 [**監視**] 底下，選擇 [**深入**解析]。 

3. 選取 [**節點**]、[**控制器**]、[**容器**] 或 [**部署（預覽）** ] 索引標籤。

4. 從 [效能] 方格中選取物件，然後在右側找到的 [屬性] 窗格上，選取 [**查看即時資料（預覽）** ] 選項。 如果 AKS 叢集是使用 Azure AD 來設定單一登入，則系統會提示您在瀏覽器會話期間第一次使用時進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >從您的 Log Analytics 工作區中，選取 [屬性] 窗格的 [**在分析中查看**] 選項來觀看資料時，記錄搜尋結果可能會顯示**節點**、背景程式**集合**、**複本集**、**作業**、 **Cron 作業** **、pod，以及可能**已不存在的**容器**。 若嘗試搜尋無法在中`kubectl`使用的容器記錄，這裡也會失敗。 請參閱[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，以深入瞭解如何查看歷程記錄、事件和計量。  

成功驗證之後，[即時資料（預覽）] 主控台窗格會出現在 [效能資料] 方格下方。 如果提取狀態指示器顯示綠色核取記號（位於窗格最右邊），表示可以抓取資料，並開始串流至您的主控台。 
    
如果您選取的物件是容器，請選取窗格中的 [**事件**] 選項。 如果您已選取節點、Pod 或控制器，則會自動選取 [查看事件]。 

![控制器屬性窗格視圖事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

窗格標題會顯示容器所群組的 Pod 名稱。

### <a name="filter-events"></a>篩選事件 

在查看事件時，您可以使用搜尋列右邊的 [**篩選**] 膠囊按鈕，進一步限制結果。 視您選取的資源而定，膠囊按鈕會列出要從中選擇的 Pod、命名空間或叢集。  

## <a name="view-metrics"></a>檢視計量 

只有選取**Pod**時，您可以從 [**節點**] 或 [**控制器**] 視圖的容器引擎產生即時度量資料。 若要查看計量，請執行下列步驟。

1. 在 [Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板的左側 [**監視**] 底下，選擇 [**深入**解析]。 

3. 選取 [**節點**] 或 [**控制器**] 索引標籤。

4. 從 [效能] 方格中選取**Pod**物件，然後在右側找到的 [屬性] 窗格中，選取 [ **View live data （預覽）** ] 選項。 如果 AKS 叢集是使用 Azure AD 來設定單一登入，則系統會提示您在瀏覽器會話期間第一次使用時進行驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >從您的 Log Analytics 工作區中，選取 [屬性] 窗格的 [**在分析中查看**] 選項來觀看資料時，記錄搜尋結果可能會顯示**節點**、背景程式**集合**、**複本集**、**作業**、 **Cron 作業** **、pod，以及可能**已不存在的**容器**。 若嘗試搜尋無法在中`kubectl`使用的容器記錄，這裡也會失敗。 請參閱[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，以深入瞭解如何查看歷程記錄、事件和計量。  

成功驗證之後，[即時資料（預覽）] 主控台窗格會出現在 [效能資料] 方格下方。 系統會抓取計量資料，並開始串流至您的主控台，以便在兩個圖表中呈現。 窗格標題會顯示容器所群組的 pod 名稱。

![View Pod 計量範例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以建立警示、視覺效果，或執行進一步的叢集分析。
