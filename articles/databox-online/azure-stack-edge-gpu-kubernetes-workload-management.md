---
title: 瞭解 Azure Stack Edge Pro 裝置上的 Kubernetes 工作負載管理 |Microsoft Docs
description: 說明如何在 Azure Stack Edge Pro 裝置上管理 Kubernetes 工作負載。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: d9e0da9e24a0bd32047d029879c4f0e110dc0c16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320790"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Kubernetes Azure Stack Edge Pro 裝置上的工作負載管理

在您的 Azure Stack Edge Pro 裝置上，當您設定計算角色時，會建立 Kubernetes 叢集。 建立 Kubernetes 叢集之後，就可以將容器化應用程式部署到 pod 中的 Kubernetes 叢集。 有不同的方式可在您的 Kubernetes 叢集中部署工作負載。 

本文說明可用來在 Azure Stack Edge Pro 裝置上部署工作負載的各種方法。

## <a name="workload-types"></a>工作負載類型

您可以在 Azure Stack Edge Pro 裝置上部署的兩個常見工作負載類型是無狀態應用程式或具狀態應用程式。

- **無狀態應用程式** 不會保留其狀態，也不會將任何資料儲存至持續性儲存體。 所有的使用者和會話資料都會與用戶端保持在一起。 無狀態應用程式的一些範例包括 web 前端（例如 Nginx）和其他 web 應用程式。

    您可以建立 Kubernetes 部署，以在叢集上部署無狀態應用程式。 

- 具**狀態應用程式**需要儲存其狀態。 具狀態應用程式會使用持續性的儲存體（例如持續性磁片區）來儲存資料，供伺服器或其他使用者使用。 具狀態應用程式的範例包括 [AZURE SQL Edge](../azure-sql-edge/overview.md) 和 MongoDB 等資料庫。

    您可以建立 Kubernetes 部署來部署具狀態應用程式。 

## <a name="deployment-flow"></a>部署流程

若要在 Azure Stack Edge Pro 裝置上部署應用程式，您將遵循下列步驟： 
 
1. **設定存取**：首先，您將使用 PowerShell 執行空間來建立使用者、建立命名空間，以及授與使用者對該命名空間的存取權。
2. **設定儲存體**：接下來，您將使用 Azure 入口網站中的 Azure Stack Edge 資源，為您將部署的具狀態應用程式，使用靜態或動態布建來建立永久性磁片區。
3. **設定網路**功能：最後，您將使用這些服務，在外部和 Kubernetes 叢集中公開應用程式。
 
## <a name="deployment-types"></a>部署類型

部署工作負載的主要方式有三種。 這兩種部署方法都可讓您連線到裝置上的相異命名空間，然後部署無狀態或具狀態的應用程式。

![Kubernetes 工作負載部署](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **本機部署**：此部署是透過命令列存取工具（例如，可 `kubectl` 讓您部署 Kubernetes） `yamls` 。 您可以透過檔案存取 Azure Stack Edge Pro 上的 Kubernetes 叢集 `kubeconfig` 。 如需詳細資訊，請移至透過 [Kubectl 存取 Kubernetes](azure-stack-edge-gpu-create-kubernetes-cluster.md)叢集。

- **IoT Edge 部署**：這是透過連接到 Azure IoT 中樞的 IoT Edge。 您可以透過命名空間連接到 Azure Stack Edge Pro 裝置上的 Kubernetes 叢集 `iotedge` 。 部署在此命名空間中的 IoT Edge 代理程式，會負責與 Azure 的連線。 您可以 `IoT Edge deployment.json` 使用 AZURE DEVOPS CI/CD 來套用設定。 命名空間和 IoT Edge 管理是透過雲端操作員進行。

- **Azure Arc 啟用的 Kubernetes 部署**： Azure Arc 啟用的 Kubernetes 是一種混合式管理工具，可讓您在 Kubernetes 叢集上部署應用程式。 您可以透過，連接到 Azure Stack Edge Pro 裝置上的 Kubernetes 叢集 `azure-arc namespace` 。 部署在此命名空間中的代理程式會負責與 Azure 的連線。 您可以使用以 Gitops) 將為基礎的設定管理來套用部署設定。 
    
    Azure Arc 啟用的 Kubernetes 也可讓您使用 Azure 監視器容器來查看和監視您的叢集。 如需詳細資訊，請移至 [Azure Arc 啟用什麼 Kubernetes？](https://docs.microsoft.com/azure/azure-arc/kubernetes/overview)。

## <a name="choose-the-deployment-type"></a>選擇部署類型

部署應用程式時，請考慮下列資訊：

- **單一或多個類型**：您可以選擇單一部署選項，或是混合不同的部署選項。
- **雲端和本機**：視您的應用程式而定，您可以透過 kubectl 或雲端部署，透過 IoT Edge 和 Azure Arc 選擇本機部署。 
    - 當您選擇本機部署時，系統會限制您的 Azure Stack Edge Pro 裝置部署所在的網路。
    - 如果您有可部署的雲端代理程式，您應該部署雲端操作員並使用雲端管理。
- **IoT 與 Azure Arc**：選擇部署也取決於您產品案例的目的。 如果您要部署的應用程式或容器與 IoT 或 IoT 生態系統有更深入的整合，請選取 IoT Edge 以部署應用程式。 如果您有現有的 Kubernetes 部署，Azure Arc 會是偏好的選擇。


## <a name="next-steps"></a>後續步驟

若要透過 kubectl 在本機部署應用程式，請參閱：

- 透過[kubectl 在您的 Azure Stack Edge Pro 上部署無狀態應用程式](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。

若要透過 IoT Edge 部署應用程式，請參閱：

- 透過[IoT Edge 在您的 Azure Stack Edge Pro 上部署範例模組](azure-stack-edge-gpu-deploy-sample-module.md)。

若要透過 Azure Arc 部署應用程式，請參閱：

- [使用 Azure Arc 部署應用程式](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)。
