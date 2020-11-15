---
title: 概述 Microsoft Azure Stack Edge Pro 裝置上的 Kubernetes 叢集 |Microsoft Docs
description: 描述如何在 Azure Stack Edge Pro 裝置上執行 Kubernetes。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 0f67a36ac4ccb27d7b955158b7e4a9cf4f5185d0
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636913"
---
# <a name="kubernetes-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 裝置上的 Kubernetes

Kubernetes 是常用的開放原始碼平臺，可協調容器化應用程式。 本文概述 Kubernetes，然後說明 Kubernetes 在您 Azure Stack Edge Pro 裝置上的運作方式。 

## <a name="about-kubernetes"></a>關於 Kubernetes 

Kubernetes 提供簡單又可靠的平臺，可管理容器型應用程式及其相關聯的網路和儲存體元件。 您可以使用 Kubernetes 快速建立、傳遞及調整容器化應用程式。

您可以使用 Kubernetes，以慣用的程式設計語言、作業系統程式庫或訊息匯流排來建立應用程式，作為開放式平臺。 若要排程和部署發行，Kubernetes 可以與現有的持續整合和持續傳遞工具整合。

如需詳細資訊，請參閱 [Kubernetes 的運作方式](https://www.youtube.com/watch?v=q1PcAawa4Bg&list=PLLasX02E8BPCrIhFrc_ZiINhbRkYMKdPT&index=2&t=0s)。

## <a name="kubernetes-on-azure-stack-edge-pro"></a>Azure Stack Edge Pro 上的 Kubernetes

在您的 Azure Stack Edge Pro 裝置上，您可以藉由設定計算來建立 Kubernetes 叢集。 設定計算角色時，系統會為您部署並設定包含主要和背景工作節點的 Kubernetes 叢集。 此叢集接著會用於透過 `kubectl` 、IoT Edge 或 Azure Arc 的工作負載部署。

Azure Stack Edge Pro 裝置可作為構成基礎結構叢集的1節點設定。 Kubernetes 叢集不同于基礎結構叢集，並部署在基礎結構叢集之上。 基礎結構叢集會為您的 Azure Stack Edge Pro 裝置提供持續性儲存體，而 Kubernetes 叢集則只負責應用程式協調流程。 

此案例中的 Kubernetes 叢集具有主要節點和背景工作節點。 叢集中的 Kubernetes 節點是執行您的應用程式和雲端工作流程的虛擬機器。 

Kubernetes 主要節點負責維護叢集的預期狀態。 主要節點也會控制接著執行容器化應用程式的背景工作節點。 

下圖說明 Kubernetes 在1個節點 Azure Stack Edge Pro 裝置上的執行。 1個節點的裝置不具高可用性，而且如果單一節點失敗，則裝置會關閉。 Kubernetes 叢集也會停止運作。

![Kubernetes Azure Stack Edge Pro 裝置的1節點架構](media/azure-stack-edge-gpu-kubernetes-overview/kubernetes-architecture-1-node.png)

如需有關 Kubernetes 叢集架構的詳細資訊，請移至 [Kubernetes 核心概念](https://kubernetes.io/docs/concepts/architecture/)。


<!--The Kubernetes cluster control plane components make global decisions about the cluster. The control plane has:

- *kubeapiserver* that is the front end of the Kubernetes API and exposes the API.
- *etcd* that is a highly available key value store that backs up all the Kubernetes cluster data.
- *kube-scheduler* that makes scheduling decisions.
- *kube-controller-manager* that runs controller processes such as those for node controllers, replications controllers, endpoint controllers, and service account and token controllers. -->

## <a name="storage-volume-provisioning"></a>儲存體磁片區布建

若要支援應用程式工作負載，您可以在 Azure Stack Edge Pro 裝置共用上掛接儲存體磁片區，以取得持續性資料。 可以使用靜態與動態磁碟區。 

如需詳細資訊，請參閱 [Azure Stack Edge Pro 裝置的 Kubernetes 儲存體](azure-stack-edge-gpu-kubernetes-storage.md)中適用于應用程式的儲存體布建選項。

## <a name="networking"></a>網路功能

Kubernetes 網路功能可讓您設定 Kubernetes 網路內的通訊，包括容器對容器網路、pod 對 pod 網路、pod 對服務網路，以及網際網路到服務的網路。 如需詳細資訊，請參閱 [Azure Stack Edge Pro 裝置之 Kubernetes 網路](azure-stack-edge-gpu-kubernetes-networking.md)中的網路功能模型。

## <a name="updates"></a>更新

當新的 Kubernetes 版本可供使用時，您的叢集可以使用 Azure Stack Edge Pro 裝置可用的標準更新進行升級。 如需有關如何升級的步驟，請參閱套用 [Azure Stack Edge Pro 的更新](azure-stack-edge-gpu-install-update.md)。

## <a name="access-monitoring"></a>存取，監視

您 Azure Stack Edge Pro 裝置上的 Kubernetes 叢集允許 Kubernetes 角色型存取控制 (Kubernetes RBAC) 。 如需詳細資訊，請參閱在 [Azure Stack Edge PRO GPU 裝置上 Kubernetes 角色型存取控制](azure-stack-edge-gpu-kubernetes-rbac.md)。

您也可以透過 Kubernetes 儀表板來監視叢集和資源的健康情況。 也可以使用容器記錄。 如需詳細資訊，請參閱 [使用 Kubernetes 儀表板來監視您 Azure Stack Edge Pro 裝置上的 Kubernetes 叢集健康](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)情況。

Azure 監視器也可作為附加元件，以從容器、節點和控制器收集健康情況資料。 如需詳細資訊，請參閱 [Azure 監視器總覽](../azure-monitor/overview.md)

<!--## Private container registry

Kubernetes on Azure Stack Edge Pro device allows for the private storage of your images by providing a local container registry.-->

## <a name="application-management"></a>應用程式管理

在 Azure Stack Edge Pro 裝置上建立 Kubernetes 叢集之後，您可以透過下列任一方法來管理此叢集上部署的應用程式：

- 原生存取 via `kubectl`
- IoT Edge 
- Azure Arc

下列各節將說明這些方法。


### <a name="kubernetes-and-kubectl"></a>Kubernetes 和 kubectl

部署 Kubernetes 叢集之後，您就可以從用戶端電腦在本機管理叢集上部署的應用程式。 您可以透過命令列使用原生工具（例如 *kubectl* ）來與應用程式互動。 

如需部署 Kubernetes 叢集的詳細資訊，請移至 [Azure Stack Edge Pro 裝置上的部署 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。 如需管理的相關資訊，請移至 [使用 kubectl 來管理您 Azure Stack Edge Pro 裝置上的 Kubernetes cluster](azure-stack-edge-gpu-create-kubernetes-cluster.md)。


### <a name="kubernetes-and-iot-edge"></a>Kubernetes 和 IoT Edge

Kubernetes 也可以與 Azure Stack Edge Pro 裝置上 IoT Edge 工作負載整合，Kubernetes 提供規模和生態系統，並提供 IoT 中心的生態系統。 Kubernetes 層用來作為基礎結構層，以部署 Azure IoT Edge 工作負載。 模組存留期和網路負載平衡是由 Kubernetes 管理，而 edge 應用程式平臺則是由 IoT Edge 管理。

如需透過 IoT Edge 在您的 Kubernetes 叢集上部署應用程式的詳細資訊，請移至： 

- 透過[IoT Edge 公開 Azure Stack Edge Pro 裝置上的無狀態應用程式](azure-stack-edge-gpu-deploy-stateless-application-iot-edge-module.md)。


### <a name="kubernetes-and-azure-arc"></a>Kubernetes 和 Azure Arc

Azure Arc 是一種混合式管理工具，可讓您在 Kubernetes 叢集上部署應用程式。 Azure Arc 也可讓您使用 Azure 監視器容器來查看和監視您的叢集。 如需詳細資訊，請移至 [Azure-Arc 啟用什麼 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。 如需 Azure Arc 定價的詳細資訊，請移至 [Azure Arc 定價](https://azure.microsoft.com/services/azure-arc/#pricing)。


## <a name="next-steps"></a>後續步驟

- 深入瞭解 [Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-kubernetes-storage.md)上的 Kubernetes 儲存體。
- 瞭解 [Azure Stack Edge Pro 裝置](azure-stack-edge-gpu-kubernetes-networking.md)上的 Kubernetes 網路模型。
- 在 Azure 入口網站中部署 [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md)。
