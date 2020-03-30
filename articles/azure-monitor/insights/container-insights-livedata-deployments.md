---
title: 查看容器部署（預覽）的 Azure 監視器 |微軟文檔
description: 本文介紹了 Kubernetes 部署的即時視圖，而不在 Azure 監視器中使用容器的 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 7d0344851e1db8c014a1bb16b228a0c2f76444d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75404764"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>如何即時查看部署（預覽）

使用容器的 Azure 監視器，視圖部署（預覽）功能通過公開`kubeclt get deployments`和`kubectl describe deployment {your deployment}`命令來即時類比對 Kubernetes 部署物件的直接存取。 

>[!NOTE]
>此功能不支援作為[專用群集](https://azure.microsoft.com/updates/aks-private-cluster/)啟用的 AKS 群集。 此功能依賴于通過瀏覽器通過代理伺服器直接存取 Kubernetes API。 啟用網路安全性來阻止 Kubernetes API 來自此代理將阻止此流量。 

>[!NOTE]
>此功能在所有 Azure 區域（包括 Azure 中國）都可用。 它當前不在 Azure 美國政府中可用。

要瞭解更多資訊，請查看庫伯奈斯關於[部署的文檔](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)。 

## <a name="how-it-works"></a>運作方式

即時資料（預覽）功能直接存取 Kubernetes API，有關身份驗證模型的其他資訊可[在此處](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。 

部署（預覽）功能對部署終結點`/apis/apps/v1/deployments`執行一次性（可刷新）負載。 它允許您選擇給定的部署，並針對部署終結點`/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}`載入該特定部署的描述詳細資訊。 

選擇頁面左上角的 **"刷新"** 會刷新部署清單。 這將類比重新運行該`kubectl`命令。 

>[!IMPORTANT]
>在此功能操作期間，不會永久存儲任何資料。 當您關閉瀏覽器或導航遠離瀏覽器時，會話期間捕獲的所有資訊都將被刪除。  

>[!NOTE]
>不能將即時資料（預覽）資料從主控台固定到 Azure 儀表板。

## <a name="deployments-describe"></a>部署描述

要查看部署的"描述詳細資訊"（相當於`kubectl describe deployment`）執行以下步驟。

1. 在 Azure 門戶中，流覽到 AKS 群集資源組並選擇 AKS 資源。

2. 在 AKS 群集儀表板上，在左側的**監視**下，選擇 **"見解**"。 

3. 選擇 **"部署（預覽）"** 選項卡。

    ![Azure 門戶中的部署視圖](./media/container-insights-livedata-deployments/deployment-view.png)

該視圖顯示所有正在運行的部署的清單以及命名空間和其他詳細資訊，類比命令`kubectl get deployments –all-namespaces`的執行。 您可以通過選擇任一列對結果進行排序。 

![部署屬性窗格詳細資訊](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

從清單中選擇部署時，屬性窗格會自動顯示在頁面右側。 它顯示與所選部署相關的資訊，如果運行命令`kubectl describe deployment {deploymentName}`，您將查看這些資訊。 您可能已經注意到描述資訊缺少一些詳細資訊。 最值得注意的是**缺少範本**。 選擇 **"原始**"選項卡，可以導航到未解析的"描述"詳細資訊。  

![部署屬性窗格原始詳細資訊](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

查看部署詳細資訊時，可以即時查看容器日誌和事件。 選擇 **"顯示即時主控台**"，"即時資料（預覽"）主控台窗格將顯示在部署資料網格下方，您可以在其中查看連續流中的即時日誌資料。 如果提取狀態指示器顯示位於窗格最右側的綠色核取記號，則意味著可以檢索資料並開始資料流到您的主控台。

還可以按命名空間或群集級別事件進行篩選。 要瞭解有關在主控台中即時查看資料的資訊，請參閱使用 Azure[監視器查看即時資料（預覽）以訪問容器](container-insights-livedata-overview.md)。 

![部署在主控台中查看即時資料](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[日誌查詢示例](container-insights-log-search.md#search-logs-to-analyze-data)以查看預定義的查詢和示例，以創建警報、視覺化或對群集執行進一步分析。
