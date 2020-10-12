---
title: Azure SQL Edge 容器的高可用性-Azure SQL Edge
description: 瞭解 Azure SQL Edge 容器的高可用性
keywords: SQL Edge、容器、高可用性
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933953"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Azure SQL Edge 容器的高可用性

在 Kubernetes 中以原生方式建立並管理您的 Azure SQL Edge 實例。 將 Azure SQL Edge 部署至受 [Kubernetes](https://kubernetes.io/)管理的 docker 容器。 在 Kubernetes 中，如果叢集節點失敗，具有 Azure SQL Edge 實例的容器就可以自動復原。 您可以使用 Kubernetes 的持續性磁片區宣告 (PVC) 來設定 SQL Edge 容器映射。 Kubernetes 會監視容器中的 Azure SQL Edge 進程。 如果處理緒、Pod、容器或節點失敗，Kubernetes 會自動啟動另一個執行個體，並重新連線至儲存體。

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Kubernetes 上的 Azure SQL Edge 容器

Kubernetes 1.6 和更新版本支援 [*儲存類別*](https://kubernetes.io/docs/concepts/storage/storage-classes/)、 [*持續性磁片區宣告*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims)。

在此設定中，Kubernetes 扮演容器協調者的角色。 

![Kubernetes 叢集中的 Azure SQL Edge 圖表](media/deploy-kubernetes/kubernetes-sql-edge.png)

在上圖中，`azure-sql-edge` 是 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/) 中的容器。 Kubernetes 會協調叢集中的資源。 [複本集](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)可確保 Pod 會在節點失敗後自動復原。 應用程式會連線至服務。 在此情況下，服務代表的負載平衡器會在 `azure-sql-edge` 失敗後維持相同 IP 位址。

在下圖中，`azure-sql-edge` 容器已失敗。 作為協調器，Kubernetes 可保證複本集中狀態良好的執行個體正確計數，並根據設定啟動新的容器。 協調器會在相同節點上啟動新的 Pod，而 `azure-sql-edge` 會重新連接到相同的永續性儲存體。 服務會連接到重新建立的 `azure-sql-edge`。

![Pod 失敗之後，Kubernetes 叢集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

在下圖中，裝載 `azure-sql-edge` 容器的節點已失敗。 協調器會在不同節點上啟動新的 Pod，而 `azure-sql-edge` 會重新連接到相同的永續性儲存體。 服務會連接到重新建立的 `azure-sql-edge`。

![節點失敗後，Kubernetes 叢集中的 Azure SQL Edge](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

若要在 Kubernetes 中建立容器，請參閱 [在 Kubernetes 中部署 AZURE SQL Edge 容器](deploy-Kubernetes.md)

## <a name="next-steps"></a>後續步驟

若要在 Azure Kubernetes Service (AKS) 中部署 Azure SQL Edge 容器，請參閱下列文章：
- [在 Kubernetes 中部署 Azure SQL Edge 容器](deploy-Kubernetes.md)
- [SQL Edge 中採用 ONNX 的機器學習與人工智慧](onnx-overview.md)。
- [使用 SQL Edge 和 IoT Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)。
- [Azure SQL Edge 中的資料串流](stream-data.md)