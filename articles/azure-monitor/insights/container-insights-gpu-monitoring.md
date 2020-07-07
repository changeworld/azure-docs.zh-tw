---
title: 使用適用于容器的 Azure 監視器來設定 GPU 監視 |Microsoft Docs
description: 本文說明如何使用適用于容器 Azure 監視器的 NVIDIA 和 AMD GPU 啟用節點來設定監視 Kubernetes 叢集。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "80373306"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>使用適用于容器的 Azure 監視器來設定 GPU 監視

從代理程式版本*ciprod03022019*開始，適用于容器的 Azure 監視器整合代理程式現在支援在 gpu 感知的 Kubernetes 叢集節點上監視 gpu （圖形處理單位）使用量，以及監視要求和使用 gpu 資源的 pod/容器。

## <a name="supported-gpu-vendors"></a>支援的 GPU 廠商

適用于容器的 Azure 監視器支援從下列 GPU 廠商監視 GPU 叢集：

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

容器的 Azure 監視器會自動開始監視節點上的 GPU 使用量，而 GPU 會要求 pod 和工作負載，方法是在60sec 間隔收集下列計量，並將它們儲存在**InsightMetrics**資料表中：

|度量名稱 |度量維度（標記） |描述 |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor|過去取樣期間（60秒）的時間百分比，在這段期間內，GPU 會忙於/主動處理容器。 「責任週期」是介於1到100之間的數位。 |
|containerGpuLimits |container.azm.ms/clusterId、container.azm.ms/clusterName、容器 |每個容器都可以將限制指定為一或多個 Gpu。 您不可能要求或限制 GPU 的一部分。 |
|containerGpuRequests |container.azm.ms/clusterId、container.azm.ms/clusterName、容器 |每個容器都可以要求一個或多個 Gpu。 您不可能要求或限制 GPU 的一部分。|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor |可供特定容器使用的 GPU 記憶體數量（以位元組為單位）。 |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId、container.azm.ms/clusterName、容器、gpuId、gpuModel、gpuVendor |特定容器所使用的 GPU 記憶體數量（以位元組為單位）。 |
|nodeGpuAllocatable |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |節點中可供 Kubernetes 使用的 Gpu 數目。 |
|nodeGpuCapacity |container.azm.ms/clusterId、container.azm.ms/clusterName、gpuVendor |節點中的 Gpu 總數。 |

## <a name="gpu-performance-charts"></a>GPU 效能圖表 

適用于容器的 Azure 監視器包含預先設定的圖表，適用于資料表先前所列的計量，做為每個叢集的 GPU 活頁簿。 您可以從 AKS 叢集直接尋找 GPU 活頁簿**節點 gpu** ，方法是選取左側窗格中的 [活頁**簿**]，然後從深入解析的 [**查看活頁簿**] 下拉式清單中。

## <a name="next-steps"></a>後續步驟

- 請參閱在 Azure Kubernetes Service （AKS）[上使用適用于計算密集型工作負載的 gpu](../../aks/gpu-cluster.md) ，以瞭解如何部署包含已啟用 GPU 之節點的 AKS 叢集。

- 深入瞭解[Microsoft Azure 中的 GPU 優化 VM sku](../../virtual-machines/sizes-gpu.md)。

- 請參閱[Kubernetes 中的 GPU 支援](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，以深入瞭解如何 Kubernetes 可在叢集中的一或多個節點上管理 gpu 的實驗性支援。