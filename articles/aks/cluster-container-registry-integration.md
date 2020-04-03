---
title: 將 Azure 容器註冊表與 Azure 庫伯奈斯服務整合
description: 瞭解如何將 Azure 庫伯奈斯服務 (AKS) 與 Azure 容器註冊表 (ACR) 整合
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 514cc25e1959145c65fe60cd3054cec4ed28f44d
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617414"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證

當您搭配使用 Azure Container Service (AKS) 與 Azure Kubernetes Registry (ACR) 時，必須建立驗證機制。 本文提供了在這兩個 Azure 服務之間配置身份驗證的範例。 

您可以使用 Azure CLI 在幾個簡單命令中設定 AKS 到 ACR 整合。 此整合將 AcrPull 角色分配給與 AKS 群集關聯的服務主體。

## <a name="before-you-begin"></a>開始之前

這些範例需要：

* **Azure 訂閱****上擁有者**或**Azure 帳戶管理員**角色
* Azure CLI 版本 2.0.73 或更高版本

為了避免需要**擁有者**或 Azure**帳戶管理員**角色,可以手動配置服務主體或使用現有服務主體從 AKS 對 ACR 進行身份驗證。 有關詳細資訊,請參閱[具有服務主體的 ACR 身份驗證](../container-registry/container-registry-auth-service-principal.md),或者[使用拉取密鑰從 Kubernetes](../container-registry/container-registry-auth-kubernetes.md)進行身份驗證。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>使用 ACR 整合新的 AKS 叢集

您可以在初始建立 AKS 群集期間設置 AKS 和 ACR 整合。  為了允許 AKS 群組與 ACR 互動,使用 Azure 活動目錄**服務主體**。 以下 CLI 命令允許您在訂閱中授權現有 ACR,並為服務主體配置相應的**ACRPull**角色。 提供下面參數的有效值。

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
$MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

或您可以使用具有以下格式的 ACR 資源 ID 指定 ACR 名稱:

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>` 

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

此步驟可能需要幾分鐘才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>為現有 AKS 叢集設定 ACR 整合

通過為**acr 名稱**或**acr 資源 id**提供有效值,將現有 ACR 與現有 AKS 群集整合。這些值如下。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acrName>
```

或者，

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

您還可以刪除 ACR 與 AKS 叢集之間的整合,如下所示

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acrName>
```

或

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>使用 ACR & AKS

### <a name="import-an-image-into-your-acr"></a>匯入影像匯入 ACR

以執行以下內容,將映像從 Docker 集線器匯入到 ACR 中:


```azurecli
az acr import  -n <myContainerRegistry> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>將範例映像從 ACR 部署到 AKS

確保您擁有正確的 AKS 認證

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

建立**名為 acr-nginx.yaml**的檔案,其中包含以下內容:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx0-deployment
  labels:
    app: nginx0-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx0
  template:
    metadata:
      labels:
        app: nginx0
    spec:
      containers:
      - name: nginx
        image: <replace this image property with you acr login server, image and tag>
        ports:
        - containerPort: 80
```

接下來,在 AKS 群組集中執行此部署:

```console
kubectl apply -f acr-nginx.yaml
```

您可以透過執行以下功能監視部署:

```console
kubectl get pods
```

您應該有兩個正在運行的窗格。

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

<!-- LINKS - external -->
[AKS AKS CLI]:  https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-create
