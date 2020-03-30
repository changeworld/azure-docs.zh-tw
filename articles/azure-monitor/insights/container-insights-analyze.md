---
title: 使用 Azure 監視器監視容器 |微軟文檔
description: 本文介紹如何使用容器的 Azure 監視器查看和分析 Kubernetes 群集的性能。
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298381"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>使用 Azure 監視器監視容器的庫伯內斯群集性能

使用容器的 Azure 監視器，可以使用性能圖表和運行狀況狀態從兩個角度監視託管在 Azure 庫伯奈斯服務 （AKS）、Azure 堆疊或其他環境上的庫伯奈斯群集的工作負荷。 可以直接從群集監視，也可以從 Azure 監視器查看訂閱中的所有群集。 監視特定 AKS 群集時，還可以查看 Azure 容器實例。

本文可説明您瞭解兩個視角，以及 Azure 監視器如何説明您快速評估、調查和解決檢測到的問題。

有關如何為容器啟用 Azure 監視器的資訊，請參閱[容器的板載 Azure 監視器](container-insights-onboard.md)。

Azure 監視器提供多群集視圖，顯示運行 Linux 和 Windows Server 2019 的所有受監視的 Kubernetes 群集的運行狀況狀態，這些群集部署在訂閱中的資源組中。 它顯示了在所有未受解決方案監視的環境中發現的群集。 您可以立即瞭解群集運行狀況，並從這裡向下切入到節點和控制器性能頁或導航以查看群集的性能圖表。 對於發現並標識為未受監視的 AKS 群集，您可以隨時啟用監視它們。 

概述[文章仲介紹了使用](container-insights-overview.md#what-does-azure-monitor-for-containers-provide)Azure 監視器監視容器的 Windows Server 群集與 Linux 群集相比的主要區別。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登錄到 Azure[門戶](https://portal.azure.com)。 

## <a name="multi-cluster-view-from-azure-monitor"></a>Azure 監視器中的多叢集檢視

要查看部署的所有 Kubernetes 群集的運行狀況狀態，請在 Azure 門戶中的左側窗格中選擇 **"監視器**"。 在 [Insights]**** 區段下方，選取 [容器]****。 

![Azure 監視器多叢集儀表板範例](./media/container-insights-analyze/azmon-containers-multiview.png)

您可以限定網格中顯示的結果的範圍，以顯示以下群集：

* **Azure** - 託管在 Azure 庫伯奈斯服務中的 AKS 和 AKS 引擎群集
* **Azure 堆疊（預覽）** - 託管在 Azure 堆疊上的 AKS 引擎群集
* **非 Azure（預覽）** - 本地託管的庫伯內斯群集
* **全部**- 查看託管在 Azure、Azure 堆疊和本地環境中的所有 Kubernetes 群集，這些群集已裝機到 Azure 監視器的容器

要查看特定環境中的群集，請從頁面左上角**的環境**PIpi 中選擇它。

![環境藥丸選擇器示例](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

在 **"監視群集"** 選項卡上，您將瞭解以下內容：

- 有多少群集處於嚴重或不正常狀態，而有多少群集處於正常或未報告狀態（稱為未知狀態）。
- 所有 Azure[庫伯奈斯引擎 （AKS 引擎）](https://github.com/Azure/aks-engine)部署是否正常。
- 每個群集部署的節點和使用者和系統窗格數。
- 有多少磁碟空間可用，以及是否有容量問題。

內含的健全狀態如下： 

* **正常**：未檢測到 VM 的問題，並且它按要求運行。 
* **嚴重**： 檢測到一個或多個關鍵問題，必須解決這些問題才能按預期恢復正常操作狀態。
* **警告**： 檢測到一個或多個問題，必須解決，否則健康情況可能變得嚴重。
* **未知**：如果服務無法與節點或 pod 建立連接，則狀態將更改為"未知"狀態。
* **未找到**：已刪除包含此解決方案工作區的工作區、資源組或訂閱。
* **未經授權的**：使用者沒有讀取工作區中資料所需的許可權。
* **錯誤**：嘗試從工作區讀取資料時出錯。
* **配置錯誤**：容器的 Azure 監視器未在指定的工作區中正確配置。
* **無資料**：過去 30 分鐘資料未向工作區報告。

運行狀況狀態將整體群集狀態計算為三種狀態*中最差的*狀態，但有一個例外。 如果三種狀態中的任何一個是"未知"，則整個群集狀態將顯示 **"未知**"。 

下表提供了控制多群集視圖上受監視群集的運行狀況的計算細目。

| |狀態 |可用性 |  
|-------|-------|-----------------|  
|**使用者窗格**| | |  
| |Healthy |100% |  
| |警告 |90 - 99% |  
| |重大 |<90% |  
| |Unknown |如果未在過去 30 分鐘內報告 |  
|**系統吊艙**| | |  
| |Healthy |100% |
| |警告 |N/A |
| |重大 |<100% |
| |Unknown |如果未在過去 30 分鐘內報告 |
|**節點** | | |
| |Healthy |>85% |
| |警告 |60 - 84% |
| |重大 |<60% |
| |Unknown |如果未在過去 30 分鐘內報告 |

從群集清單中，可以通過選擇群集的名稱向下切入到**群集**頁面。 然後，通過選擇該特定群集的 **"節點**"列中的節點匯總，轉到 **"節點**性能"頁。 或者，您可以通過選擇 **"使用者"窗格**或**系統窗格**列的匯總來向下切入到 **"控制器**性能"頁。

## <a name="view-performance-directly-from-a-cluster"></a>直接從群集查看性能

通過從左側窗格中選擇 **"見解群集"，** > **Cluster**或者從多群集視圖中選擇群集，可以直接從 AKS 群集訪問容器的 Azure 監視器。 有關群集的資訊分為四個透視：

- 叢集
- 節點 
- Controllers 
- 容器

>[!NOTE]
>本文其餘部分中描述的體驗也適用于查看從多群集視圖選擇時託管在 Azure Stack 或其他環境中的 Kubernetes 群集的性能和運行狀況狀態。 

預設頁面將打開並顯示四個線性能圖表，顯示群集的關鍵性能指標。 

![[叢集] 索引標籤上的範例效能圖表](./media/container-insights-analyze/containers-cluster-perfview.png)

性能圖表顯示四個性能指標：

- **節點 CPU&nbsp;利用率**：整個群集 CPU 利用率的聚合視角。 要篩選時間範圍的結果，請在圖表上方的百分位數選擇器中選擇 **"平均**數"、**最小值**、**第 50、90、95****或** **Max。** **90th** 篩檢程式可以單獨使用或組合使用。 
- **節點記憶體利用率&nbsp;**：整個群集記憶體利用率的聚合視角。 要篩選時間範圍的結果，請在圖表上方的百分位數選擇器中選擇 **"平均**數"、**最小值**、**第 50、90、95****或** **Max。** **90th** 篩檢程式可以單獨使用或組合使用。 
- **節點計數**：Kubernetes 中的節點計數和狀態。 表示的叢集節點的狀態為"總計"、就緒和未就緒。 它們可以單獨篩選或在圖表上方的選擇器中組合。 
- **活動窗格計數**：來自庫伯奈斯的窗格計數和狀態。 表示的窗格的狀態為"總計"、"掛起"、"正在運行"、"未知"、"成功"或"失敗"。 它們可以單獨篩選或在圖表上方的選擇器中組合。 

使用"左箭頭"和"右箭頭"鍵迴圈流覽圖表上的每個資料點。 使用向上和向下方向鍵迴圈穿過百分線。 選擇任一圖表右上角的針腳圖示，將所選圖表固定到您查看的最後一個 Azure 儀表板。 在儀表板中，您可以調整圖表的大小和重新置放。 從儀表板中選擇圖表會將您重定向到容器的 Azure 監視器，並載入正確的範圍和視圖。

容器的 Azure 監視器還支援 Azure 監視器[指標資源管理器](../platform/metrics-getting-started.md)，您可以在其中創建自己的繪圖圖表、關聯和調查趨勢以及固定到儀表板。 從指標資源管理器中，您還可以使用設置的條件將指標視覺化為[基於指標的警報規則](../platform/alerts-metric.md)的基礎。 

## <a name="view-container-metrics-in-metrics-explorer"></a>在指標資源管理器中查看容器指標

在指標資源管理器中，可以從 Azure 監視器查看容器的聚合節點和窗格利用率指標。 下表總結了詳細資訊，以説明您瞭解如何使用指標圖表視覺化容器指標。

|命名空間 | 計量 | 描述 | 
|----------|--------|-------------|
| 見解.容器/節點 | |
| | cpuUsageMillicores | 聚合測量整個群集的 CPU 利用率。 它是一個 CPU 內核，分裂為 1000 個單位（毫 = 1000）。 用於確定容器中內核的使用方式，其中許多應用程式可能使用一個內核。| 
| | cpu使用百分比 | 聚合的平均 CPU 利用率以整個群集的百分比為單位。|
| | 記憶體乘數 | 以位元組為單位使用的容器 RSS 記憶體。| 
| | 記憶體百分比 | 以百分比表示使用的容器 RSS 記憶體。|
| | 記憶體工作集位元組 | 使用的容器工作集記憶體。| 
| | 記憶體工作集百分比 | 容器工作集記憶體以百分比表示使用。 | 
| | 節點計數 | 來自庫伯內斯的節點計數。|
| 見解.容器/pods | |
| | PodCount | 庫伯內斯的吊艙計數。|

您可以[拆分](../platform/metrics-charts.md#apply-splitting-to-a-chart)指標以按維度查看指標，並視覺化指標的不同段彼此比較情況。 對於節點，您可以按*主機*維度對圖表進行分段。 從窗格中，您可以按以下維度進行分段：

* 控制器
* 庫伯內斯命名空間
* 節點
* 階段

## <a name="analyze-nodes-controllers-and-container-health"></a>分析節點、控制器和容器運行狀況

切換到 **"節點**、**控制器**"和 **"容器**"選項卡時，屬性窗格會自動顯示在頁面右側。 它顯示所選項目的屬性，其中包括為組織 Kubernetes 物件而定義的標籤。 選擇 Linux 節點時，"**本地磁片容量**"部分還會顯示可用磁碟空間和用於向該節點顯示的每個磁片的百分比。 選擇**>>** 窗格中的連結以查看或隱藏窗格。

當您展開階層中的物件時，屬性窗格會根據所選的物件更新。 還可以通過選擇窗格頂部的 **"查看即時資料（預覽）"** 連結，查看 Kubernetes 容器日誌（stdout/stderror）、事件和 pod 指標。 有關授予和控制查看此資料所需的配置的詳細資訊，請參閱[設置即時資料（預覽）。](container-insights-livedata-setup.md) 查看群集資源時，可以從容器即時看到此資料。 有關此功能的詳細資訊，請參閱[如何即時查看 Kubernetes 日誌、事件和 pod 指標](container-insights-livedata-overview.md)。 要根據預定義的日誌搜索查看存儲在工作區中的 Kubernetes 日誌資料，請選擇"在分析下拉清單中**的視圖"中****查看容器日誌**。 有關本主題的其他資訊，請參閱[搜索日誌以分析資料](container-insights-log-search.md#search-logs-to-analyze-data)。

使用頁面頂部的 **"添加篩選器"** 選項按**服務**、**節點**、**命名空間**或**節點池**篩選視圖的結果。 選擇篩選器範圍後，選擇 **"選擇值"** 欄位中顯示的值之一。 配置篩選器後，它將全域應用，同時查看 AKS 群集的任何透視。 此公式僅支援等號。 您可以在第一個篩選器上方新增其他篩選器，進一步縮小您的結果。 例如，如果按**Node**指定篩選器，則只能為第二個篩選器選擇**服務**或**命名空間**。

選擇另一個選項卡時，將繼續應用一個選項卡中指定篩選器。 選擇指定篩選器旁邊的**x**符號後，將刪除該符號。 

切換到 **"節點"** 選項卡，行層次結構遵循 Kubernetes 物件模型，該模型從群集中的節點開始。 展開節點以查看在節點上運行的一個或多個窗格。 如果多個容器分組到窗格中，它們將顯示為層次結構中的最後一行。 如果主機具有處理器或記憶體壓力，還可以查看主機上運行的與 pod 無關的工作負載數。

![效能檢視中庫伯內斯節點層次結構的示例](./media/container-insights-analyze/containers-nodes-view.png)

運行 Windows Server 2019 作業系統的 Windows Server 容器在清單中所有基於 Linux 的節點後顯示。 展開 Windows Server 節點時，可以查看在節點上運行的一個或多個窗格和容器。 選擇節點後，屬性窗格將顯示版本資訊。 由於 Windows Server 節點未安裝代理，因此排除了代理資訊。 

![示例列出 Windows 伺服器節點的節點層次結構](./media/container-insights-analyze/nodes-view-windows.png) 

運行 Linux 作業系統的 Azure 容器實例虛擬節點顯示在清單中最後一個 AKS 叢集節點之後。 展開容器實例虛擬節點時，可以查看在節點上運行的一個或多個容器實例窗格和容器。 不會為節點收集和報告指標，僅針對窗格。

![列出容器執行個體的範例節點階層](./media/container-insights-analyze/nodes-view-aci.png)

從擴展節點中，可以從在節點上運行的 Pod 或容器向下切入到控制器以查看為該控制器篩選的效能資料。 選擇特定節點的 **"控制器**"列下的值。
 
![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-node-controller.png)

選擇頁面頂部的控制器或容器，以查看這些物件的狀態和資源利用率。 要查看記憶體利用率，請在 **"指標**"下拉清單中選擇記憶體**RSS**或**記憶體工作集**。 只有 Kubernetes 1.8 版和更新版本才支援**記憶體 RSS**。 否則，您將**&nbsp;Min**的值視為*NaN&nbsp;*，這是表示不可定義或不可表示值的數位資料類型值。

![容器節點效能檢視](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**記憶體工作集**同時顯示包含的駐留記憶體和虛擬記憶體（緩存），是應用程式正在使用的全部內容。 **記憶體 RSS**僅顯示主記憶體（換句話說，它只不過是駐留記憶體）。 此指標顯示可用記憶體的實際容量。 駐留記憶體和虛擬記憶體之間的區別是什麼？

- 駐留記憶體或主記憶體，是叢集節點可用的電腦記憶體的實際量。

- 虛擬記憶體是作業系統使用的保留硬碟空間（緩存），用於在記憶體壓力下將資料從記憶體交換到磁片，然後在需要時將其取回到記憶體。

預設情況下，效能資料基於過去六個小時，但您可以使用左上角的**TimeRange**選項更改視窗。 您還可以通過在百分位數選擇器中選擇 **"最小**值"、Avg、50、90、95**和**** ** **Max**來篩選時間範圍內的結果。 **Avg** **90th** 

![資料篩選的百分位數選取範圍](./media/container-insights-analyze/containers-metric-percentile-filter.png)

將滑鼠懸停在 **"趨勢"** 列下的橫條圖上時，每個橫條圖都會在 15 分鐘內顯示 CPU 或記憶體使用方式，具體取決於所選指標。 通過鍵盤選擇趨勢圖後，請使用 Alt_Page 上鍵或 Alt_Page 向下鍵單獨迴圈流覽每個橫條圖。 當您將滑鼠懸停在欄上時，您將獲得與您相同的詳細資訊。

![趨勢橫條圖懸停示例](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

在下一個示例中，對於清單中的第一個節點*aks-nodepool1-，***容器**的值為 9。 此值是部署的容器總數的匯總。

![每個節點的容器匯總示例](./media/container-insights-analyze/containers-nodes-containerstotal.png)

此資訊可以説明您快速確定群集中的節點之間是否有適當的容器平衡。 

下表描述了查看 **"節點"** 選項卡時顯示的資訊。

| 資料行 | 描述 | 
|--------|-------------|
| 名稱 | 主機的名稱。 |
| 狀態 | 節點狀態的 Kubernetes 檢視。 |
| 最小&nbsp;， 平均&nbsp;， 50%，&nbsp;90%，&nbsp;&nbsp;95%， 最大&nbsp;%  | 根據選取期間內百分位數的平均節點百分比。 |
| 最小， 平均， 50， 90， 95， 最大 | 根據所選持續時間內百分位數計算節點的實際值。 平均值是從節點設置的 CPU/記憶體限制中測量的。 對於窗格和容器，它是主機報告的平均值。 |
| 容器 | 容器的數目。 |
| Uptime | 呈現自節點啟動或重新啟動以來經過的時間。 |
| 控制器 | 僅適用於容器與 Pod。 它顯示它駐留在哪個控制器。 並非所有 Pod 都位在控制器之中，因此有些可能會顯示 **N/A**。 | 
| 趨勢最小&nbsp;， 平均&nbsp;，&nbsp;50%， 90%，&nbsp;95%，&nbsp;最大&nbsp;% | 長條圖趨勢代表控制器的平均百分位數計量百分比。 |

在展開名為 **"其他進程**"的節點後，您可能會注意到工作負荷。 它表示在節點上運行的非容器化進程，包括：

* 自我管理或管理的庫伯奈斯非容器化流程

* 容器運行時進程  

* Kubelet  

* 在節點上運行的系統進程

* 在節點硬體或 VM 上運行的其他非庫伯內特工作負載

它計算的公式為：從 - *容器化流程的*CAdvisor 使用率*總用量*。  

在選取器中，選取 [控制器]****。

![選擇控制器視圖](./media/container-insights-analyze/containers-controllers-tab.png)

在這裡，您可以查看控制器和容器實例虛擬節點控制器或未連接到控制器的虛擬節點窗格的性能運行狀況。

![\<名稱>控制器效能檢視](./media/container-insights-analyze/containers-controllers-view.png)

行層次結構以控制器開頭。 展開控制器時，可以查看一個或多個窗格。 展開 Pod，最後一個資料夾會顯示分組至 Pod 的容器。 從擴展的控制器，可以向下切入到它運行的節點以查看為該節點篩選的效能資料。 未連接到控制器的容器實例窗格在清單中最後列出。

![列出容器執行個體 Pod 的範例控制器階層](./media/container-insights-analyze/controllers-view-aci.png)

選擇特定控制器的 **"節點**"列下的值。

![效能檢視中從節點到控制器的向下切入範例](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看控制器時顯示的資訊。

| 資料行 | 描述 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器在完成運行後的狀態（如 *"確定*"、*終止*、*失敗*、*已停止*或*暫停*）的匯總狀態。 如果容器正在運行，但狀態未正確顯示或代理未拾取，且超過 30 分鐘未回應，則狀態為 *"未知*"。 下表提供了狀態圖示的其他詳細資訊。|
| 最小&nbsp;， 平均&nbsp;， 50%，&nbsp;90%，&nbsp;&nbsp;95%， 最大&nbsp;%| 針對所選取計量和百分位數之每個實體的平均百分比彙總平均值。 |
| 最小， 平均， 50， 90， 95， 最大  | 容器針對所選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| 容器 | 該控制器或 Pod 的容器總數。 |
| Restarts | 容器重新啟動計數的彙總。 |
| Uptime | 代表自容器啟動以來經過的時間。 |
| 節點 | 僅適用於容器與 Pod。 它顯示它駐留在哪個控制器。 | 
| 趨勢最小&nbsp;， 平均&nbsp;，&nbsp;50%， 90%，&nbsp;95%，&nbsp;最大&nbsp;% | 長條圖趨勢代表控制器的平均百分位數計量。 |

狀態欄位中的圖示指示容器的線上狀態。
 
| 圖示 | 狀態 | 
|--------|-------------|
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|
| ![等待或暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次報告運行，但回應超過 30 分鐘|
| ![成功狀態圖示](./media/container-insights-analyze/containers-green-icon.png) | 已成功停止或無法停止|

狀態圖示會根據 Pod 所提供的功能顯示計數。 它會顯示最差的兩個狀態，且當您將滑鼠暫留在狀態上方時，會顯示容器中所有 Pod 的彙總狀態。 如果沒有就緒狀態，則狀態值會顯示 **(0)**。

在選取器中，選取 [容器]****。

![選擇容器視圖](./media/container-insights-analyze/containers-containers-tab.png)

您可以在這裡檢視 Azure Kubernetes 和 Azure 容器執行個體容器的效能健康情況。 

![\<名稱>容器效能檢視](./media/container-insights-analyze/containers-containers-view.png)

您可以從容器向下切入到 Pod 或節點，以檢視針對該物件所篩選的效能資料。 選擇特定容器的 **"Pod"** 或 **"節點"** 列下的值。

![效能檢視中從節點向下切入的示例](./media/container-insights-analyze/drill-down-controller-node.png)

下表描述了查看容器時顯示的資訊。

| 資料行 | 描述 | 
|--------|-------------|
| 名稱 | 控制器的名稱。|
| 狀態 | 容器的狀態，若有的話。 下一個表格會提供狀態圖示的其他詳細資料。|
| 最小&nbsp;， 平均&nbsp;， 50%，&nbsp;90%，&nbsp;&nbsp;95%， 最大&nbsp;% | 針對所選取計量和百分位數之每個實體的平均百分比彙總。 |
| 最小， 平均， 50， 90， 95， 最大 | 容器針對所選取百分位數的平均 CPU millicore 或記憶體效能彙總。 平均值是從為 Pod 設定的 CPU/記憶體限制測量所得。 |
| Pod | Pod 所在的容器。| 
| 節點 |  容器所在的節點。 | 
| Restarts | 代表自容器啟動以來經過的時間。 |
| Uptime | 代表自容器啟動或重新啟動以來經過的時間。 |
| 趨勢最小&nbsp;， 平均&nbsp;，&nbsp;50%， 90%，&nbsp;95%，&nbsp;最大&nbsp;% | 長條圖趨勢代表容器的平均百分位數計量百分比。 |

狀態欄位中的圖示指示 pod 的線上狀態，如下表所述。
 
| 圖示 | 狀態 |  
|--------|-------------|  
| ![準備好執行的狀態圖示](./media/container-insights-analyze/containers-ready-icon.png) | 執行中 (就緒)|  
| ![等待或暫停狀態圖示](./media/container-insights-analyze/containers-waiting-icon.png) | 等候中或已暫停|  
| ![上次回報的執行狀態圖示](./media/container-insights-analyze/containers-grey-icon.png) | 上次回報的執行，但未回應的時間超過 30 分鐘|  
| ![終止的狀態圖示](./media/container-insights-analyze/containers-terminated-icon.png) | 已成功停止或無法停止|  
| ![失敗狀態圖示](./media/container-insights-analyze/containers-failed-icon.png) | 失敗狀態 |  

## <a name="workbooks"></a>活頁簿

活頁簿將文本、 [日誌查詢](../log-query/query-language.md)、[指標](../platform/data-platform-metrics.md)和參數合併為豐富的互動式報表。 活頁簿可以由具有相同 Azure 資源存取權的其他小組成員編輯。

容器的 Azure 監視器包括四個活頁簿，用於説明您入門：

- **磁片容量**：通過以下視角顯示每個磁片的互動式磁片使用方式圖表：

    - 所有磁片的磁片使用百分比。
    - 所有磁片的可用磁碟空間。
    - 顯示每個節點的磁片、其已用空間百分比、可用空間百分比趨勢、可用磁碟空間 （GiB） 和可用磁碟空間趨勢 （GiB） 的網格。 在表中選擇行時，已用空間和可用磁碟空間 （GiB） 的百分比將顯示在行下方。 

- **磁片 IO**： 通過以下透視圖顯示每個磁片的互動式磁片利用率圖表， 顯示到容器內的節點：

    - 磁片 I/O 按讀取位元組/秒匯總所有磁片，寫入位元組/秒，以及讀取和寫入位元組/秒趨勢。
    - 八個性能圖表顯示關鍵性能指標，以説明測量和識別磁片 I/O 瓶頸。

- **Kubelet**： 包括兩個顯示關鍵節點操作統計資訊的網格：

    - 按節點網格概覽按每個節點的百分比和趨勢匯總總操作、總錯誤和成功操作。
    - 按操作類型概述，按百分比和趨勢匯總每個操作的總操作、總錯誤和成功操作。

- **網路**：為每個節點的網路介面卡提供互動式網路利用率圖表，網格顯示關鍵性能指標，以説明測量網路介面卡的性能。

您可以通過從 **"查看活頁簿"** 下拉清單中選擇每個活頁簿來訪問這些活頁簿。

![查看活頁簿下拉清單](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>後續步驟

- 查看[使用 Azure 監視器為容器創建性能警報](container-insights-alerts.md)，瞭解如何為高 CPU 和記憶體利用率創建警報以支援 DevOps 或操作過程和過程。

- 查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以評估或自訂以提醒、視覺化或分析群集。

- 查看[監視器群集運行狀況](container-insights-health.md)，瞭解查看 Kubernetes 群集的運行狀況。
