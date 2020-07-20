---
title: 查看容器部署的 Azure 監視器（預覽） |Microsoft Docs
description: 本文說明 Kubernetes 部署的即時觀點，而不在容器的 Azure 監視器中使用 kubectl。
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2f1eac82ce67818c7bf86ce3ca8924155d8ee2aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337991"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>如何即時查看部署（預覽）

使用適用于容器的 Azure 監視器，視圖部署（預覽）功能會公開和命令，即時模擬 Kubernetes 部署物件的直接 `kubeclt get deployments` 存取 `kubectl describe deployment {your deployment}` 。

>[!NOTE]
>此功能不支援已啟用為[私人](https://azure.microsoft.com/updates/aks-private-cluster/)叢集的 AKS 叢集。 此功能依賴從瀏覽器透過 proxy 伺服器直接存取 Kubernetes API。 啟用網路安全性以封鎖此 proxy 的 Kubernetes API 將會封鎖此流量。

若要深入瞭解，請參閱有關[部署](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)的 Kubernetes 檔。

## <a name="how-it-works"></a>運作方式

即時資料（預覽）功能會直接存取 Kubernetes API，而有關驗證模型的其他資訊則可以在[這裡](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)找到。

部署（預覽）功能會對部署端點執行一次（可重新整理）負載 `/apis/apps/v1/deployments` 。 它可讓您選取指定的部署，並針對部署端點載入該特定部署的描述詳細資料 `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` 。

選取**頁面**左上角的 [重新整理]，會重新整理部署清單。 這會模擬重新執行 `kubectl` 命令。

>[!IMPORTANT]
>在此功能的操作期間，不會永久儲存任何資料。 當您關閉瀏覽器或從中流覽時，會刪除會話期間所捕捉到的所有資訊。

>[!NOTE]
>您無法將即時資料（預覽）資料從主控台釘選到 Azure 儀表板。

## <a name="deployments-describe"></a>部署描述

若要查看部署的描述詳細資料（相當於）， `kubectl describe deployment` 請執行下列步驟。

1. 在 [Azure 入口網站中，流覽至 AKS 叢集資源群組，然後選取您的 AKS 資源。

2. 在 AKS 叢集儀表板的左側 [**監視**] 底下，選擇 [**深入**解析]。

3. 選取 [**部署（預覽）** ] 索引標籤。

    ![Azure 入口網站中的部署視圖](./media/container-insights-livedata-deployments/deployment-view.png)

此視圖會顯示所有正在執行的部署清單，以及命名空間和其他詳細資訊，以模擬命令的執行 `kubectl get deployments –all-namespaces` 。 您可以選取任何一個資料行來排序結果。

![部署屬性窗格詳細資料](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

當您從清單中選取部署時，[屬性] 窗格會自動顯示在頁面的右側。 它會顯示您執行命令時所要查看之所選部署的相關資訊 `kubectl describe deployment {deploymentName}` 。 您可能已注意到描述資訊缺少一些詳細資料。 最值得注意的是**範本**遺失。 選取 [**原始**] 索引標籤可讓您流覽至未剖析的描述詳細資料。

![部署屬性窗格原始詳細資料](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

當您查看部署詳細資料時，您可以即時看到容器記錄和事件。 選取 [**顯示即時主控台**]，[即時資料（預覽）] 主控台窗格會出現在 [部署] 資料方格下方，您可以在其中查看連續資料流程中的即時記錄資料。 如果提取狀態指示器顯示綠色核取記號（位於窗格最右邊），表示可以抓取資料，並開始串流至您的主控台。

您也可以依命名空間或叢集層級事件進行篩選。 若要深入瞭解主控台中的即時查看資料，請參閱[使用適用于容器的 Azure 監視器來觀看即時資料（預覽）](container-insights-livedata-overview.md)。

![部署在主控台中查看即時資料](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>後續步驟

- 若要繼續了解如何使用 Azure 監視器並監視您 AKS 叢集的其他層面，請參閱[檢視 Azure Kubernetes 服務健康情況](container-insights-analyze.md)。

- 查看[記錄查詢範例](container-insights-log-search.md#search-logs-to-analyze-data)，以查看預先定義的查詢和範例，以建立警示、視覺效果，或執行進一步的叢集分析。
