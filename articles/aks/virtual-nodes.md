---
title: 使用虛擬節點
titleSuffix: Azure Kubernetes Service
description: '概述如何搭配使用虛擬節點與 Azure Kubernetes Services (AKS) '
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 1e5a53c80aed90e44ba594dcff3c8ca23afc2c4f
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684769"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>建立並設定 Azure Kubernetes Services (AKS) 叢集以使用虛擬節點

若要快速調整 AKS 叢集中的應用程式工作負載，您可以使用虛擬節點。 透過虛擬節點，您可以快速佈建 Pod，而且只需在節點執行時付費 (以秒計算)。 您不需要等候 Kubernetes 叢集自動調整程式來部署 VM 計算節點以執行其他 Pod。 僅 Linux Pod 和節點支援虛擬節點。

適用于 AKS 的虛擬節點附加元件是以開放原始碼專案 [虛擬 Kubelet][virtual-kubelet-repo]為基礎。

本文概述使用虛擬節點的區域可用性和網路需求，以及已知的限制。

## <a name="regional-availability"></a>區域可用性

支援 VNET Sku 的所有區域都支援虛擬節點部署。

針對每個區域中可用的 CPU 和記憶體 Sku，請檢查 azure [區域中 Azure 容器實例的 Azure 容器實例資源可用性-Linux 容器群組](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>網路需求

虛擬節點能在於 Azure 容器執行個體 (ACI) 與 AKS 叢集中執行的 Pod 之間啟用網路通訊。 為了提供此通訊功能，需要建立虛擬網路子網路並指派委派權限。 虛擬節點只適用于使用 *advanced* 網路 (Azure CNI) 所建立的 AKS 叢集。 根據預設，系統會使用 *基本* 網路 (kubenet) 來建立 AKS 叢集。

在 Azure 容器實例中執行的 pod (ACI) 需要 AKS API 伺服器端點的存取權，才能設定網路功能。

## <a name="known-limitations"></a>已知限制

虛擬節點功能非常依賴 ACI 的功能集。 除了 [Azure 容器實例的配額和限制](../container-instances/container-instances-quotas.md)之外，虛擬節點尚未支援下列案例：

* 使用服務主體來提取 ACR 映像。 [因應措施](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) \(英文\) 是使用 [Kubernetes 祕密](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line) \(英文\)
* [虛擬網路限制](../container-instances/container-instances-vnet.md)包括 VNet 對等互連、Kubernetes 網路原則，以及搭配網路安全性群組的網際網路輸出流量。
* 初始容器
* [主機別名](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/) \(英文\)
* 適用於 ACI 中 exec 的[引數](../container-instances/container-instances-exec.md#restrictions)
* [Daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) 不會將 pod 部署到虛擬節點
* 虛擬節點支援對 Linux Pod 進行排程。 您可以手動安裝開放原始碼 [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) \(英文\) 提供者，以針對 ACI 對 Windows Server 容器進行排程。
* 虛擬節點需要 AKS 叢集搭配 Azure CNI 網路。
* 具有私人叢集的虛擬節點。
* 使用 api 伺服器授權的 ip 範圍進行 AKS。
* 磁片區掛接 Azure 檔案儲存體共用支援 [一般用途 V1](../storage/common/storage-account-overview.md#types-of-storage-accounts)。 遵循[使用 Azure 檔案儲存體共用載入磁片區](azure-files-volume.md)的指示
* 不支援使用 IPv6。

## <a name="next-steps"></a>下一步

為您的叢集設定虛擬節點：

- [使用 Azure CLI 建立虛擬節點](virtual-nodes-cli.md)
- [在 Azure Kubernetes Service (AKS) 中使用入口網站建立虛擬節點](virtual-nodes-portal.md)

虛擬節點往往是 AKS 中調整解決方案的一個元件。 如需有關調整解決方案的詳細資訊，請參閱下列文章：

- [使用 Kubernetes 水平 Pod 自動調整器][aks-hpa]
- [使用 Kubernetes 叢集自動調整器][aks-cluster-autoscaler]
- [查看適用於虛擬節點的自動調整範例][virtual-node-autoscale]
- [深入了解 Virtual Kubelet 開放原始碼程式庫][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
