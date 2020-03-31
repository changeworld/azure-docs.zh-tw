---
title: 使用 Azure 監視器配置容器的 GPU 監視 |微軟文檔
description: 本文介紹如何使用啟用 NVIDIA 和 AMD GPU 的節點配置監視庫伯奈斯群集，以及用於容器的 Azure 監視器。
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373306"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>使用 Azure 監視器配置容器的 GPU 監視

從代理版本*ciprod03022019*開始，用於容器集成代理的 Azure 監視器現在支援監視 GPU 感知 Kubernetes 叢集節點上的 GPU（圖形處理單元）使用方式，並監視請求和使用 GPU 資源的 pod/容器。

## <a name="supported-gpu-vendors"></a>支援的 GPU 供應商

容器的 Azure 監視器支援監視來自以下 GPU 供應商的 GPU 群集：

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

容器的 Azure 監視器通過每隔 60 秒收集以下指標並將其存儲在**InsightMetrics**表中，自動開始監視節點上的 GPU 使用方式，GPU 請求窗格和工作負載：

|度量名稱 |指標維度（標籤） |描述 |
|------------|------------------------|------------|
|容器Gpu任務迴圈 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名稱，gpuId，gpuModel，供應商 gpu|GPU 繁忙/主動處理容器的過去採樣期（60 秒）的百分比。 占空比是介於 1 和 100 之間的數位。 |
|容器 Gpu 限制 |container.azm.ms/clusterId、container.azm.ms/clusterName、容器名稱 |每個容器都可以將限制指定為一個或多個 GPU。 無法請求或限制 GPU 的一小部分。 |
|容器 Gpu 請求 |container.azm.ms/clusterId、container.azm.ms/clusterName、容器名稱 |每個容器可以請求一個或多個 GPU。 無法請求或限制 GPU 的一小部分。|
|容器 Gpu 記憶體總位元組 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名稱，gpuId，gpuModel，供應商 gpu |可用於特定容器的 GPU 記憶體量（以位元組為單位）。 |
|容器 Gpu 記憶使用位元組 |container.azm.ms/clusterId，container.azm.ms/clusterName，容器名稱，gpuId，gpuModel，供應商 gpu |特定容器使用的 GPU 記憶體量（以位元組為單位）。 |
|節點GpuAlloca可 |container.azm.ms/clusterId、container.azm.ms/clusterName、gpu 供應商 |Kubernetes 可以使用的節點中的 GPU 數。 |
|節點 Gpu 容量 |container.azm.ms/clusterId、container.azm.ms/clusterName、gpu 供應商 |節點中的 GPU 總數。 |

## <a name="gpu-performance-charts"></a>GPU 性能圖表 

容器的 Azure 監視器包括表前面列出的指標的預配置圖表，作為每個群集的 GPU 活頁簿。 您可以通過從左側窗格中選擇**活頁簿**，以及從"透視"中的 **"查看活頁簿"** 下拉清單中直接從 AKS 群集中找到 GPU 活頁簿**節點 GPU。**

## <a name="next-steps"></a>後續步驟

- 請參閱在 Azure Kubernetes 服務 （AKS）[上使用 GPU 進行計算密集型工作負荷](../../aks/gpu-cluster.md)，瞭解如何部署包含啟用 GPU 的節點的 AKS 群集。

- 詳細瞭解微軟[Azure 中的 GPU 優化 VM SKU](../../virtual-machines/sizes-gpu.md)。

- 查看[Kubernetes 中的 GPU 支援](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)，瞭解有關 Kubernetes 在群集中一個或多個節點管理 GPU 的實驗支援。