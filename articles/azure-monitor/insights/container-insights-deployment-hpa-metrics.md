---
title: 部署 & 使用容器的 Azure 監視器 HPA 計量 |Microsoft Docs
description: 本文說明使用容器 Azure 監視器收集) 計量的 & HPA (水準 pod 自動調整程式的部署。
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: ee1f0d4849a8382a898aaca84956ff78166e138f
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570441"
---
# <a name="deployment--hpa-metrics-with-azure-monitor-for-containers"></a>部署 & 使用容器的 Azure 監視器 HPA 計量

從代理程式版本 *ciprod08072020*開始，適用于容器的 Azure 監視器-整合式代理程式現在會收集部署 & hpa 的計量。

## <a name="deployment-metrics"></a>部署計量

容器的 Azure 監視器會依60秒間隔收集下列計量，並將其儲存在 **InsightMetrics** 資料表中，以自動開始監視部署：

|度量名稱 |) 的計量維度 (標記 |描述 |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、deployment、deploymentStrategy、k8sNamespace、spec_replicas、status_replicas_available status_replicas_updated (status。 updatedReplicas)  | 此部署的目標就緒 pod 總數 (readyReplicas) 。 以下是此度量的維度。 <ul> <li> 部署-部署的名稱 </li> <li> k8sNamespace-Kubernetes 部署的命名空間 </li> <li> deploymentStrategy-用來以新的 pod 取代 pod 的部署策略 (規格。類型) </li><li> creationTime-部署建立時間戳記 </li> <li> spec_replicas-所需的 pod 數目 (規格)  </li> <li>status_replicas_available-可用 pod 的總數 (可供此部署的目標至少 minReadySeconds)  (狀態。 availableReplicas) </li><li>status_replicas_updated-此部署的目標非終止 pod 總數，其具有所需的範本規格 (狀態 updatedReplicas)  </li></ul>|

## <a name="hpa-metrics"></a>HPA 計量

針對容器 Azure 監視器會以60秒間隔收集下列計量，並將其儲存在 **InsightMetrics** 資料表中，以自動開始監視 hpa：

|度量名稱 |) 的計量維度 (標記 |描述 |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId、container.azm.ms/clusterName、creationTime、hpa、k8sNamespace、lastScaleTime、spec_max_replicas、spec_min_replicas、status_desired_replicas、targetKind、targetName | 目前由此自動調整程式管理的 pod 複本數目 (currentReplicas) 。 以下是此度量的維度。 <ul> <li> hpa-HPA 的名稱 </li> <li> k8sNamespace-Kubernetes 的 HPA 命名空間 </li> <li> lastScaleTime-上次 HPA 調整 pod 數目的時間 (狀態。 lastScaleTime) </li><li> creationTime-HPA 建立時間戳記 </li> <li> spec_max_replicas-可由自動調整程式 (規格. maxReplicas 設定的 pod 數目上限)  </li> <li> spec_min_replicas-可將自動調整程式縮減 (minReplicas 的複本數目下限)  </li><li>status_desired_replicas-此自動調整程式所管理的 pod 複本數目 (desiredReplicas) </li><li>targetKind-HPA 的目標 (規格. scaleTargetRef. 種類)  </li><li>targetName-HPA 的目標 (spec.scaleTargetRef.name 的名稱)  </li></ul>|

## <a name="deployment--hpa-charts"></a>部署 & HPA 圖 

容器的 Azure 監視器包括先前在資料表中為每個叢集的活頁簿所列的計量預先設定的圖表。 您可以從左側窗格中選取活頁**簿**，以及從深入解析的 [**查看活頁簿**] 下拉式清單中，找到部署 & HPA 活頁簿**部署 &** 直接從 AKS 叢集 HPA。

## <a name="next-steps"></a>接下來的步驟

- 請參閱 [Kubernetes 中的 Kube 狀態計量](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) ，以深入瞭解 Kube 狀態度量。