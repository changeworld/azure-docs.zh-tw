---
title: (已被取代) Azure Container Service 教學課程 - 部署叢集
description: Azure Container Service 教學課程 - 部署叢集
author: iainfoulds
ms.service: container-service
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b8821f3bb3d48786697cbc4137baf530856774fd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "78274013"
---
# <a name="deprecated-deploy-a-kubernetes-cluster-in-azure-container-service"></a>(已被取代) 在 Azure Container Service 中部署 Kubernetes 叢集

> [!TIP]
> 如需使用 Azure Kubernetes Service 的本教學課程更新版本，請參閱[教學課程：部署 Azure Kubernetes Service (AKS) 叢集](../../aks/tutorial-kubernetes-deploy-cluster.md)。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes 會提供容器化應用程式的分散式平台。 透過 Azure Container Service，可簡單又快速地佈建生產環境就緒 Kubernetes 叢集。 在本教學課程的第 3 和第 7 部分中，已部署一個 Azure Container Service Kubernetes 叢集。 完成的步驟包括：

> [!div class="checklist"]
> * 部署 Kubernetes ACS 叢集
> * 安裝 Kubernetes CLI (kubectl)
> * 設定 kubectl

在後續的教學課程中，會將 Azure Vote 應用程式部署至叢集、並相應放大和更新，且會將 Log Analytics 設定為監視 Kubernetes 叢集。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已建立容器映像並上傳到 Azure Container Registry 執行個體。 如果您尚未完成這些步驟，而想要跟著做，請回到[教學課程 1 – 建立容器映像](./container-service-tutorial-kubernetes-prepare-app.md)。

## <a name="create-kubernetes-cluster"></a>建立 Kubernetes 叢集

使用 [az acs create](/cli/azure/acs#az-acs-create) 命令，在 Azure Container Service 中建立 Kubernetes 叢集。 

下列範例會在名為 `myResourceGroup` 的資源群組中，建立名為 `myK8sCluster` 的叢集。 我們已在[先前的教學課程](./container-service-tutorial-kubernetes-prepare-acr.md)中建立此資源群組。

```azurecli-interactive
az acs create --orchestrator-type kubernetes --resource-group myResourceGroup --name myK8SCluster --generate-ssh-keys 
```

在有限試用之類的某些情況下，Azure 訂用帳戶只擁有 Azure 資源的有限存取權。 如果部署因可用核心受限而失敗，請將 `--agent-count 1` 加入 [az acs create](/cli/azure/acs#az-acs-create) 命令來減少預設代理程式的數量。 

幾分鐘之後，部署就會完成，並以 json 格式傳回 ACS 部署的相關資訊。

## <a name="install-the-kubectl-cli"></a>安裝 kubectl CLI

若要從用戶端電腦連線到 Kubernetes 叢集，請使用 [kubectl](https://kubernetes.io/docs/user-guide/kubectl/) (Kubernetes 命令列用戶端)。 

如果您是使用 Azure Cloud Shell，則已安裝 kubectl。 如果您想要在本機進行安裝，請使用 [az acs kubernetes install-cli](/cli/azure/acs/kubernetes) 命令。

如果執行環境是 Linux 或 macOS，則可能需要使用 sudo 來執行。 在 Windows 上，請確定您的殼層已經是以系統管理員身分執行。

```azurecli-interactive
az acs kubernetes install-cli 
```

在 Windows 上，預設安裝是 *c:\program files (x86)\kubectl.exe*。 您可能需要將此檔案新增到 Windows 路徑。 

## <a name="connect-with-kubectl"></a>使用 kubectl 連線

若要設定 kubectl 來連線到 Kubernetes 叢集，請執行 [az acs kubernetes get-credentials](/cli/azure/acs/kubernetes) 命令。

```azurecli-interactive
az acs kubernetes get-credentials --resource-group myResourceGroup --name myK8SCluster
```

若要確認與叢集的連線，請執行 [kubectl get nodes](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) 命令。

```console
kubectl get nodes
```

輸出：

```output
NAME                    STATUS                     AGE       VERSION
k8s-agent-98dc3136-0    Ready                      5m        v1.6.2
k8s-agent-98dc3136-1    Ready                      5m        v1.6.2
k8s-agent-98dc3136-2    Ready                      5m        v1.6.2
k8s-master-98dc3136-0   Ready,SchedulingDisabled   5m        v1.6.2
```

完成教學課程時，您的 ACS Kubernetes 叢集便已可供工作負載使用。 在後續的教學課程中，會將多容器應用程式部署到這個叢集、向外延展、更新，以及進行監視。

## <a name="next-steps"></a>後續步驟

在本教學課程中，已部署一個 Azure Container Service Kubernetes 叢集。 已完成下列步驟：

> [!div class="checklist"]
> * 已部署 Kubernetes ACS 叢集
> * 已安裝 Kubernetes CLI (kubectl)
> * 已設定 kubectl

請前往下一個教學課程，以了解如何在叢集上執行應用程式。

> [!div class="nextstepaction"]
> [在 Kubernetes 中部署應用程式](./container-service-tutorial-kubernetes-deploy-application.md)
