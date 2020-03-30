---
title: 概念 - 在 Azure Kubernetes Service (AKS) 中調整應用程式
description: 了解 Azure Kubernetes Service (AKS) 中的調整功能，包括水平 Pod 自動調整程式、叢集自動調整程式，以及 Azure 容器執行個體連接器。
services: container-service
author: zr-msft
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: zarhoads
ms.openlocfilehash: 396e5bc31723768ada334dd5043bca724af5e84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595853"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中調整應用程式的選項

當您在 Azure Kubernetes Service (AKS) 中執行應用程式時，可能需要增加或減少計算資源的數量。 當您需要的應用程式執行個體數目有所變更時，基礎 Kubernetes 節點的數目可能也需要變更。 您可能還需要快速預配大量其他應用程式實例。

本文所介紹的核心概念，可協助您在 AKS 中調整應用程式：

- [手動調整](#manually-scale-pods-or-nodes)
- [水平 Pod 自動調整程式 (HPA)](#horizontal-pod-autoscaler)
- [叢集自動調整程式](#cluster-autoscaler)
- [將 AKS 與 Azure 容器執行個體 (ACI) 整合](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>手動調整 Pod 或節點

您可以手動調整複本 (Pod) 及節點，來測試您的應用程式如何回應可用資源和狀態中的變更。 手動調整資源也可讓您定義用來維護固定成本的資源集數量，例如節點的數目。 要手動縮放，請定義副本或節點計數。 然後，Kubernetes API 會根據該副本或節點計數計畫創建其他 pod 或排空節點。

向下縮放節點時，Kubernetes API 呼叫與群集使用的計算類型綁定的相關 Azure 計算 API。 例如，對於基於 VM Scale 構建的群集，設置用於選擇要刪除的節點的邏輯由 VM Scale Set API 確定。 要瞭解有關如何選擇節點以在縮小規模時刪除的更多，請參閱[VMSS 常見問題解答](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-faq#if-i-reduce-my-scale-set-capacity-from-20-to-15-which-vms-are-removed)。

若要開始手動調整 Pod 和節點，請參閱[在 AKS 中調整應用程式][aks-scale]。

## <a name="horizontal-pod-autoscaler"></a>水平 Pod 自動調整程式

Kubernetes 會使用水平 Pod 自動調整程式 (HPA) 來監視資源需求，並自動調整複本數目。 根據預設，水平 Pod 自動調整程式每隔 30 秒會檢查計量 API，找出複本計數中任何必要的變更。 需要進行變更時，複本的數目會據以增加或減少。 水平 Pod 自動調整程式會搭配使用 AKS 叢集，當中已部署適用於 Kubernetes 1.8 + 的計量伺服器。

![Kubernetes 水平 Pod 自動調整](media/concepts-scale/horizontal-pod-autoscaling.png)

當您針對指定的部署設定水平 Pod 自動調整程式時，需定義可執行複本的最小和最大數目。 您也會根據任何調整決策定義要監視的計量，例如 CPU 使用量。

若要開始使用 AKS 中的水平 Pod 自動調整程式，請參閱 [AKS 中的自動調整 Pod][aks-hpa]。

### <a name="cooldown-of-scaling-events"></a>調整事件中的冷卻時間

因為水平 Pod 自動調整程式每隔 30 秒會檢查計量 API，因此在進行另一項檢查之前，先前的調整事件可能尚未成功完成。 此行為可能導致水準 pod 自動縮放程式更改複製副本的數量，而上一個比例事件可能會接收應用程式工作負載，並且需要相應地進行調整。

為了最小化這些比賽事件，請設置冷卻或延遲值。 這些值會定義水平 Pod 自動調整程式在調整事件之後，必須等待多久時間，才可以觸發另一個調整事件。 此行為允許新的副本計數生效，並且指標 API 反映分散式工作負載。 根據預設，相應增加事件的延遲為 3 分鐘，而相應減少事件的延遲為 5 分鐘

目前，您無法從預設值中調整這些冷卻值。

## <a name="cluster-autoscaler"></a>叢集自動調整程式

為了回應不斷變化的 pod 需求，Kubernetes 具有群集自動縮放器，該自動縮放器根據節點池中請求的計算資源調整節點數。 預設情況下，群集自動縮放器每 10 秒檢查一次指標 API 伺服器，以檢查節點計數中所需的任何更改。 如果叢集自動調整判斷需要變更，則您 AKS 叢集中的節點數目會據以增加或減少。 叢集自動調整程式會使用已啟用 RBAC 功能且執行 Kubernetes 1.10.x 或更高版本的 AKS 叢集。

![Kubernetes 叢集自動調整程式](media/concepts-scale/cluster-autoscaler.png)

叢集自動調整程式通常會與水平 Pod 自動調整程式一起使用。 加以結合時，水平 Pod 自動調整程式會根據應用程式需求來增加或減少 Pod 的數目，而叢集自動調整程式會據此來調整所需的節點數目，以執行這些額外的 Pod。

若要在 AKS 中開始使用叢集自動調整程式，請參閱 [AKS 上的叢集自動調整程式][aks-cluster-autoscaler]。

### <a name="scale-up-events"></a>相應增加事件

如果節點沒有足夠的計算資源來運行請求的 Pod，則該 pod 無法通過計畫過程進行。 除非節點池中提供了其他計算資源，否則無法啟動 pod。

當群集自動縮放器注意到由於節點池資源約束而無法安排的 pod 時，節點池中的節點數會增加以提供額外的計算資源。 當這些額外的節點已成功部署且可供在節點集區內使用時，就會將 Pod 排程在節點上執行。

如果您必須快速調整應用程式，某些 Pod 會維持在等候排程的狀態，直到由叢集自動調整程式部署的其他節點可以接受排程的 Pod 為止。 對於具有高載需求的應用程式，您可以使用虛擬節點與 Azure 容器執行個體進行調整。

### <a name="scale-down-events"></a>相應減少事件

群集自動縮放器還監視最近未收到新計畫請求的節點的 Pod 調度狀態。 此方案指示節點池的計算資源超過所需資源，並且節點數可以減少。

依預設，節點若超過 10 分鐘未使用的閾值，則會安排將此節點刪除。 當這種情況發生時，Pod 會排程在節點集區內的其他節點上執行，且叢集自動調整程式會減少節點的數目。

因為叢集自動調整程式在減少節點的數目時，Pod 會排程在不同節點上，所以您的應用程式可能會遇到一些中斷情況。 若要盡可能不中斷，請避免使用單一 Pod 執行個體的應用程式。

## <a name="burst-to-azure-container-instances"></a>高載至 Azure 容器執行個體

若要快速調整您的 AKS 叢集，您可以與 Azure 容器執行個體 (ACI) 整合。 Kubernetes 有內建的元件，可調整複本和節點計數。 不過，如果您的應用程式需要快速調整，水平 Pod 自動調整程式可能排程更多 Pod，比節點集區中現有計算資源所能提供的還要多。 如果設定完成，則這種情況可能會觸發叢集自動調整程式在節點集區中部署其他節點，但是這些節點可能需要幾分鐘的時間才能成功佈建，並允許 Kubernetes 排程器在節點上執行 Pod。

![Kubernetes 高載調整至 ACI](media/concepts-scale/burst-scaling.png)

ACI 可讓您快速部署容器執行個體，不需要額外的基礎結構成本。 當您與 AKS 連線時，ACI 會變成您 AKS 叢集的安全邏輯擴充功能。 基於[虛擬庫貝萊特][virtual-kubelet]的[虛擬節點][virtual-nodes-cli]元件安裝在 AKS 群集中，該群集將 ACI 作為虛擬 Kubernetes 節點呈現。 接著，Kubernetes 可以排程透過虛擬節點以 ACI 執行個體身分執行的 Pod，而非直接在 AKS 叢集中，以 VM 節點上的 Pod 身分執行。 虛擬節點當前處於 AKS 中的預覽狀態。

您的應用程式不需要進行任何修改即可使用虛擬節點。 部署可以跨 AKS 和 ACI 調整，且當叢集自動調整程式在 AKS 叢集中部署新的節點時沒有延遲。

虛擬節點會部署到與您 AKS 叢集相同虛擬網路中的其他子網路。 此虛擬網路設定可維護 ACI 與 AKS 之間流量的安全。 如同 AKS 叢集，ACI 執行個體是安全的邏輯計算資源，會與其他使用者隔離。

## <a name="next-steps"></a>後續步驟

若要開始使用調整應用程式，首先請遵循[使用 Azure CLI 建立 AKS 叢集的快速入門][aks-quickstart]。 接著，您可以在 AKS 叢集中開始手動或自動調整應用程式：

- 手動調整 [Pod][aks-manually-scale-pods] 或[節點][aks-manually-scale-nodes]
- 使用[水平 Pod 自動調整程式][aks-hpa]
- 使用[叢集自動調整程式][aks-cluster-autoscaler]

如需有關 Kubernetes 及 AKS 核心概念的詳細資訊，請參閱下列文章：

- [Kubernetes / AKS 叢集和工作負載][aks-concepts-clusters-workloads]
- [Kubernetes / AKS 存取和身分識別][aks-concepts-identity]
- [Kubernetes / AKS 安全性][aks-concepts-security]
- [Kubernetes / AKS 虛擬網路][aks-concepts-network]
- [Kubernetes / AKS 儲存體][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md