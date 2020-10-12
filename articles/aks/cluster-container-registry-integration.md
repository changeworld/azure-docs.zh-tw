---
title: 整合 Azure Container Registry 與 Azure Kubernetes Service
description: '瞭解如何整合 Azure Kubernetes Service (AKS) 與 Azure Container Registry (ACR) '
services: container-service
manager: gwallace
ms.topic: article
ms.date: 02/25/2020
ms.openlocfilehash: 4338f4ce1fe60a3a9002be93feab134dd2601720
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87406498"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>從 Azure Kubernetes Service 對 Azure Container Registry 進行驗證

當您搭配使用 Azure Container Service (AKS) 與 Azure Kubernetes Registry (ACR) 時，必須建立驗證機制。 這項作業會在 CLI 和入口網站體驗中執行，方法是將所需的許可權授與您的 ACR。 本文提供在這兩個 Azure 服務之間設定驗證的範例。 

您可以使用 Azure CLI，以幾個簡單的命令來設定 ACR 整合的 AKS。 此整合會將 AcrPull 角色指派給與 AKS 叢集相關聯的服務主體。

## <a name="before-you-begin"></a>開始之前

這些範例需要：

* **Azure 訂**用帳戶上的**擁有**者或**azure 帳戶管理員**角色
* Azure CLI 2.7.0 版或更新版本

若要避免需要 **擁有** 者或 **Azure 帳戶管理員** 角色，您可以手動設定服務主體，或使用現有的服務主體從 AKS 驗證 ACR。 如需詳細資訊，請參閱[使用服務主體進行 ACR 驗證](../container-registry/container-registry-auth-service-principal.md)或[使用提取密碼從 Kubernetes 進行驗證](../container-registry/container-registry-auth-kubernetes.md)。

## <a name="create-a-new-aks-cluster-with-acr-integration"></a>使用 ACR 整合建立新的 AKS 叢集

您可以在最初建立 AKS 叢集期間設定 AKS 和 ACR 整合。  若要允許 AKS 叢集與 ACR 互動，請使用 Azure Active Directory **服務主體** 。 下列 CLI 命令可讓您在訂用帳戶中授權現有的 ACR，並為服務主體設定適當的 **ACRPull** 角色。 為您的參數提供有效的值。

```azurecli
# set this to the name of your Azure Container Registry.  It must be globally unique
MYACR=myContainerRegistry

# Run the following line to create an Azure Container Registry if you do not already have one
az acr create -n $MYACR -g myContainerRegistryResourceGroup --sku basic

# Create an AKS cluster with ACR integration
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr $MYACR
```

或者，您可以使用 ACR 資源識別碼來指定 ACR 名稱，其格式如下：

`/subscriptions/\<subscription-id\>/resourceGroups/\<resource-group-name\>/providers/Microsoft.ContainerRegistry/registries/\<name\>`

> [!NOTE]
> 如果您使用的 ACR 位於 AKS 叢集以外的不同訂用帳戶中，請在從 AKS 叢集附加或卸離時使用 ACR 資源識別碼。

```azurecli
az aks create -n myAKSCluster -g myResourceGroup --generate-ssh-keys --attach-acr /subscriptions/<subscription-id>/resourceGroups/myContainerRegistryResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry
```

此步驟可能需要幾分鐘的時間才能完成。

## <a name="configure-acr-integration-for-existing-aks-clusters"></a>設定現有 AKS 叢集的 ACR 整合

提供 **acr 名稱** 或 **acr 資源識別碼** 的有效值（如下所示），將現有的 ACR 與現有的 AKS 叢集整合。

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-name>
```

或者，

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --attach-acr <acr-resource-id>
```

您也可以使用下列方式移除 ACR 與 AKS 叢集之間的整合

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-name>
```

或

```azurecli
az aks update -n myAKSCluster -g myResourceGroup --detach-acr <acr-resource-id>
```

## <a name="working-with-acr--aks"></a>使用 ACR & AKS

### <a name="import-an-image-into-your-acr"></a>將映射匯入 ACR

執行下列程式，將映射從 docker hub 匯入您的 ACR：


```azurecli
az acr import  -n <acr-name> --source docker.io/library/nginx:latest --image nginx:v1
```

### <a name="deploy-the-sample-image-from-acr-to-aks"></a>從 ACR 將範例映射部署至 AKS

確定您有適當的 AKS 認證

```azurecli
az aks get-credentials -g myResourceGroup -n myAKSCluster
```

建立一個名為 **nginx yaml** 的檔案，其中包含下列各項。 以您的登錄資源名稱取代 **acr 名稱**。 範例： *>mycontainerregistry*。

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
        image: <acr-name>.azurecr.io/nginx:v1
        ports:
        - containerPort: 80
```

接下來，在您的 AKS 叢集中執行此部署：

```console
kubectl apply -f acr-nginx.yaml
```

您可以執行下列動作來監視部署：

```console
kubectl get pods
```

您應該有兩個執行中的 pod。

```output
NAME                                 READY   STATUS    RESTARTS   AGE
nginx0-deployment-669dfc4d4b-x74kr   1/1     Running   0          20s
nginx0-deployment-669dfc4d4b-xdpd6   1/1     Running   0          20s
```

### <a name="troubleshooting"></a>疑難排解
* 深入瞭解 [ACR 診斷](../container-registry/container-registry-diagnostics-audit-logs.md)
* 深入瞭解 [ACR 健全狀況](../container-registry/container-registry-check-health.md)

<!-- LINKS - external -->
[AKS AKS CLI]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
