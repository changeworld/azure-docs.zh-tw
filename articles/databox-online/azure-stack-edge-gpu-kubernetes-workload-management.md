---
title: 瞭解 Azure Stack Edge 裝置上的 Kubernetes 工作負載管理 |Microsoft Docs
description: 說明如何在 Azure Stack Edge 裝置上管理 Kubernetes 工作負載。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 21845b51fdd108221d5e1bce50e953b79084d17d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/28/2020
ms.locfileid: "89083396"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-device"></a>Kubernetes Azure Stack Edge 裝置上的工作負載管理

在您的 Azure Stack Edge 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 建立 Kubernetes 叢集之後，就可以將容器化應用程式部署到 pod 中的 Kubernetes 叢集。 有不同的方式可在您的 Kubernetes 叢集中部署工作負載。 

本文說明可用於在 Azure Stack Edge 裝置上部署工作負載的各種方法。

## <a name="workload-types"></a>工作負載類型

您可以在 Azure Stack Edge 裝置上部署的兩個常見工作負載類型是無狀態應用程式或可設定狀態的應用程式。

- **無狀態應用程式** 不會保留其狀態，也不會將任何資料儲存至持續性儲存體。 所有的使用者和會話資料都會與用戶端保持在一起。 無狀態應用程式的一些範例包括 web 前端（例如 Nginx）和其他 web 應用程式。

    您可以建立 Kubernetes 部署，以在叢集上部署無狀態應用程式。 

- 具**狀態應用程式**需要儲存其狀態。 具狀態應用程式會使用持續性的儲存體（例如持續性磁片區）來儲存資料，供伺服器或其他使用者使用。 具狀態應用程式的範例包括 MongoDB 等資料庫。

    您可以建立 Kubernetes 部署來部署具狀態應用程式。 

## <a name="namespaces-types"></a>命名空間類型

Kubernetes 資源（例如 pod 和部署）會以邏輯方式分組到命名空間中。 這些群組可讓您以邏輯方式分割 Kubernetes 叢集，並限制存取以建立、查看或管理資源。 使用者只能與其指派的命名空間內包含的資源互動。

命名空間適用于有許多使用者散佈在多個小組或專案的環境中。 對於少數到數十個使用者的叢集，您應該完全不需要建立或考慮命名空間。 當您需要所提供的功能時，請開始使用命名空間。

如需詳細資訊，請參閱 [Kubernetes 命名空間](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)。


您的 Azure Stack Edge 裝置具有下列命名空間：

- **系統命名空間** -此命名空間是核心資源所在的位置，例如 DNS 和 proxy 等網路功能，或 Kubernetes 儀表板。 您通常不會將自己的應用程式部署到此命名空間中。 使用此命名空間來偵測任何 Kubernetes 叢集問題。 

    您的裝置上有多個系統命名空間，並保留對應于這些系統命名空間的名稱。 以下是保留的系統命名空間清單： 
    - kube-系統
    - metallb-系統
    - dbe-命名空間
    - default
    - kubernetes-儀表板
    - default
    - kube-節點-租用
    - kube-public
    - iotedge
    - azure-arc

    請務必不要針對您所建立的使用者命名空間使用任何保留名稱。 
<!--- **default namespace** - This namespace is where pods and deployments are created by default when none is provided and you have admin access to this namespace. When you interact with the Kubernetes API, such as with `kubectl get pods`, the default namespace is used when none is specified.-->

- **使用者命名空間** -這些是您可以透過 **kubectl** 建立以在本機部署應用程式的命名空間。
 
- **IoT Edge 命名空間** -您會連接到此 `iotedge` 命名空間，以透過 IoT Edge 部署應用程式。

- **Azure Arc 命名空間** -您會連接到此 `azure-arc` 命名空間，以透過 Azure Arc 部署應用程式。

 
## <a name="deployment-types"></a>部署類型

部署工作負載的主要方式有三種。 這兩種部署方法都可讓您連線到裝置上的相異命名空間，然後部署無狀態或具狀態的應用程式。

![Kubernetes 工作負載部署](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **本機部署**：這是透過命令列存取工具 `kubectl` ，例如，可讓您部署 K8 `yamls` 。 您可以使用檔案在您所建立的 Azure Stack Edge 上連接到 K8 叢集 `kubeconfig` 。 如需詳細資訊，請移至透過 [Kubectl 存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。

- **IoT Edge 部署**：這是透過連接到 Azure IoT 中樞的 IoT Edge。 您可以透過命名空間連接到 Azure Stack Edge 裝置上的 K8 叢集 `iotedge` 。 部署在此命名空間中的 IoT Edge 代理程式，會負責與 Azure 的連線。 您可以 `IoT Edge deployment.json` 使用 AZURE DEVOPS CI/CD 來套用設定。 命名空間和 IoT Edge 管理是透過雲端操作員進行。

- **Azure/Arc 部署**： Azure Arc 是一種混合式管理工具，可讓您在 K8 叢集上部署應用程式。 您可以透過在 Azure Stack Edge 裝置上連接 K8 叢集 `azure-arc namespace` 。  代理程式會部署在此命名空間中，負責與 Azure 的連線。 您可以使用以 Gitops) 將為基礎的設定管理來套用部署設定。 Azure Arc 也可讓您使用 Azure 監視器容器來查看和監視您的叢集。 如需詳細資訊，請移至 [什麼是已啟用 Azure Arc 的 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。

## <a name="choose-the-deployment-type"></a>選擇部署類型

部署應用程式時，請考慮下列資訊：

- **單一或多個類型**：您可以選擇單一部署選項，或是混合不同的部署選項。
- **雲端和本機**：視您的應用程式而定，您可以透過 kubectl 或雲端部署，透過 IoT Edge 和 Azure Arc 選擇本機部署。 
    - 本機部署較適用于開發案例。 當您選擇本機部署時，系統會限制您的 Azure Stack Edge 裝置部署所在的網路。
    - 如果您有可部署的雲端代理程式，您應該部署雲端操作員並使用雲端管理。
- **IoT 與 Azure Arc**：選擇部署也取決於您產品案例的目的。 如果您要部署的應用程式或容器與 IoT 或 IoT 生態系統有更深入的整合，則您應該挑選部署應用程式的 IoT Edge 方式。 如果您有現有的 Kubernetes 部署，Azure Arc 會是偏好的選擇。


## <a name="next-steps"></a>後續步驟

若要透過 kubectl 在本機部署應用程式，請參閱：

- 透過[kubectl 在您的 Azure Stack Edge 上部署無狀態應用程式](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

若要透過 IoT Edge 部署應用程式，請參閱：

- 透過[IoT Edge 將範例模組部署在您的 Azure Stack Edge 上](azure-stack-edge-gpu-deploy-sample-module.md)。

若要透過 Azure Arc 部署應用程式，請參閱：

- [使用 Azure Arc 部署應用程式](azure-stack-edge-gpu-deploy-sample-module.md)。
