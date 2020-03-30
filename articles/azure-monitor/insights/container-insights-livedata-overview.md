---
title: 使用 Azure 監視器查看容器的即時資料（預覽） |微軟文檔
description: 本文介紹了 Kubernetes 日誌、事件和 pod 指標的即時視圖，而無需在 Azure 監視器中使用容器的 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 9e7c7a7b7bf276b3451cee1d289b8b07ac0f40ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216558"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>如何即時查看 Kubernetes 日誌、事件和 pod 指標

容器的 Azure 監視器包括即時資料（預覽）功能，這是一個高級診斷功能，允許您直接存取 Azure Kubernetes 服務 （AKS） 容器日誌 （stdout/stderror）、事件和 pod 指標。 它公開對`kubectl logs -c`的`kubectl get`直接存取 ，事件`kubectl top pods`和 。 主控台窗格顯示容器引擎生成的日誌、事件和指標，以進一步説明即時解決問題。

本文提供了詳細的概述，並説明您瞭解如何使用此功能。 

>[!NOTE]
>此功能不支援作為[專用群集](https://azure.microsoft.com/updates/aks-private-cluster/)啟用的 AKS 群集。 此功能依賴于通過瀏覽器通過代理伺服器直接存取 Kubernetes API。 啟用網路安全性來阻止 Kubernetes API 來自此代理將阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 區域（包括 Azure 中國）都可用。 它當前不在 Azure 美國政府中可用。

有關設置或排除即時資料（預覽）功能的説明，請查看我們的[設置指南](container-insights-livedata-setup.md)。 此功能直接存取 Kubernetes API，有關身份驗證模型的其他資訊可[在此處](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

## <a name="live-data-preview-functionality-overview"></a>即時資料（預覽）功能概述

### <a name="search"></a>搜尋

![即時資料主控台窗格篩選器示例](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

即時資料（預覽）功能包括搜索功能。 在 **"搜索"** 欄位中，可以通過鍵入一個關鍵字或術語來篩選結果，並突出顯示任何匹配的結果以允許快速查看。 查看事件時，還可以使用搜索欄右側的 **"篩選器**"PI，進一步限制結果。 根據您選擇的資源，該藥片列出了要從中選擇的 Pod、命名空間或群集。  

### <a name="scroll-lock-and-pause"></a>滾動鎖定和暫停 

要掛起自動滾動並控制窗格的行為，允許您手動滾動遍讀的新資料，可以使用 **"滾動"** 選項。 要重新啟用自動滾動，只需再次選擇 **"滾動"** 選項即可。 您還可以通過選擇 **"暫停"** 選項暫停日誌或事件資料的檢索，當您準備好恢復時，只需選擇 **"播放**"。  

![即時資料主控台窗格暫停即時視圖](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>我們建議在疑難排解時僅暫停或暫停自動滾動短時間。 這些請求可能會影響群集上的 Kubernetes API 的可用性和限制。 

>[!IMPORTANT]
>在此功能操作期間，不會永久存儲任何資料。 當您關閉瀏覽器或導航遠離瀏覽器時，會話期間捕獲的所有資訊都將被刪除。 資料僅保留在指標要素的五分鐘視窗內進行視覺化;任何超過五分鐘的指標也會被刪除。 即時資料（預覽）緩衝區查詢在合理的記憶體使用限制內（需要在這裡更具體，什麼是合理的？ 

## <a name="view-logs"></a>檢視記錄

您可以查看即時日誌資料，因為它們由容器引擎從**節點**、**控制器**和**容器**視圖生成。 要查看日誌資料，請執行以下步驟。

1. 在 Azure 門戶中，流覽到 AKS 群集資源組並選擇 AKS 資源。

2. 在 AKS 群集儀表板上，在左側的**監視**下，選擇 **"見解**"。 

3. 選擇"**節點**、**控制器**"或 **"容器"** 選項卡。

4. 從性能網格中選擇物件，在右側找到的屬性窗格上，選擇 **"查看即時資料（預覽）"** 選項。 如果 AKS 群集配置了使用 Azure AD 的單一登入，系統將提示您在該瀏覽器會話期間首次使用時進行身份驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >通過從屬性窗格中選擇 **"分析中的視圖"** 選項來查看日誌分析工作區中的資料時，日誌搜尋結果可能會顯示可能不再存在的**節點**、**守護進程集**、**複本集**、**作業****、Cron 作業****、Pod**和**容器**。 嘗試搜索日誌以查找不在 的`kubectl`容器也會失敗。 查看[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，瞭解有關查看歷史日誌、事件和指標的更多資訊。  

成功驗證後，"即時資料（預覽"）主控台窗格將顯示在效能資料網格下方，您可以在其中查看連續流的日誌資料。 如果提取狀態指示器顯示位於窗格最右側的綠色核取記號，則意味著可以檢索資料並開始資料流到您的主控台。  

![節點屬性窗格視圖資料選項](./media/container-insights-livedata-overview/node-properties-pane.png)  

窗格標題顯示容器分組的窗格的名稱。

## <a name="view-events"></a>檢視事件

當選擇容器、pod、節點、複本集、守護蒙集、作業、CronJob 或部署時，可以從容器引擎從 **"節點**、**控制器**、**容器**和**部署（預覽）"** 視圖中查看它們生成的即時事件資料。 要查看事件，執行以下步驟。

1. 在 Azure 門戶中，流覽到 AKS 群集資源組並選擇 AKS 資源。

2. 在 AKS 群集儀表板上，在左側的**監視**下，選擇 **"見解**"。 

3. 選擇 **"節點**、**控制器**、**容器**"或 **"部署（預覽）"** 選項卡。

4. 從性能網格中選擇物件，在右側找到的屬性窗格上，選擇 **"查看即時資料（預覽）"** 選項。 如果 AKS 群集配置了使用 Azure AD 的單一登入，系統將提示您在該瀏覽器會話期間首次使用時進行身份驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >通過從屬性窗格中選擇 **"分析中的視圖"** 選項來查看日誌分析工作區中的資料時，日誌搜尋結果可能會顯示可能不再存在的**節點**、**守護進程集**、**複本集**、**作業****、Cron 作業****、Pod**和**容器**。 嘗試搜索日誌以查找不在 的`kubectl`容器也會失敗。 查看[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，瞭解有關查看歷史日誌、事件和指標的更多資訊。  

成功驗證後，"即時資料（預覽"）主控台窗格將顯示在效能資料網格下方。 如果提取狀態指示器顯示位於窗格最右側的綠色核取記號，則意味著可以檢索資料並開始資料流到您的主控台。 
    
如果所選物件是容器，請在窗格中選擇"**事件**"選項。 如果選擇了節點、Pod 或控制器，則會自動選擇查看事件。 

![控制器屬性窗格視圖事件](./media/container-insights-livedata-overview/controller-properties-live-event.png)  

窗格標題顯示容器分組的 Pod 的名稱。

### <a name="filter-events"></a>篩選事件 

查看事件時，還可以使用搜索欄右側的 **"篩選器**"PI，進一步限制結果。 根據您選擇的資源，該藥片列出了要從中選擇的 Pod、命名空間或群集。  

## <a name="view-metrics"></a>檢視計量 

您可以查看即時指標資料，因為它們由容器引擎從**節點**或**控制器**視圖中生成，僅當選擇**Pod**時。 要查看指標，執行以下步驟。

1. 在 Azure 門戶中，流覽到 AKS 群集資源組並選擇 AKS 資源。

2. 在 AKS 群集儀表板上，在左側的**監視**下，選擇 **"見解**"。 

3. 選擇"**節點**"或"**控制器"** 選項卡。

4. 從性能網格中選擇**Pod**物件，在右側找到的屬性窗格上，選擇 **"查看即時資料（預覽）"** 選項。 如果 AKS 群集配置了使用 Azure AD 的單一登入，系統將提示您在該瀏覽器會話期間首次使用時進行身份驗證。 選取您的帳戶，然後向 Azure 完成驗證。  

    >[!NOTE]
    >通過從屬性窗格中選擇 **"分析中的視圖"** 選項來查看日誌分析工作區中的資料時，日誌搜尋結果可能會顯示可能不再存在的**節點**、**守護進程集**、**複本集**、**作業****、Cron 作業****、Pod**和**容器**。 嘗試搜索日誌以查找不在 的`kubectl`容器也會失敗。 查看[分析中的視圖](container-insights-log-search.md#search-logs-to-analyze-data)功能，瞭解有關查看歷史日誌、事件和指標的更多資訊。  

成功驗證後，"即時資料（預覽"）主控台窗格將顯示在效能資料網格下方。 將檢索指標資料，並開始資料流到您的主控台，以便在兩個圖表中進行演示。 窗格標題顯示容器分組的窗格的名稱。

![查看 Pod 指標示例](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)  

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以創建警報、視覺化或對群集執行進一步分析。
