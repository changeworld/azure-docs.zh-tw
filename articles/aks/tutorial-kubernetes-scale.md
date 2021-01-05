---
title: Azure 上的 Kubernetes 教學課程 - 調整應用程式
description: 在本 Azure Kubernetes Service (AKS) 教學課程中，您將了解如何在 Kubernetes 中調整節點和 Pod，以及實作水平 Pod 自動調整。
services: container-service
ms.topic: tutorial
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 7f16ba3ffe6b6f96f17df540eb67e9cec0bfea8c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825685"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>教學課程：調整 Azure Kubernetes Service (AKS) 中的應用程式

如果您已依照教學課程操作，就會在 AKS 中有一個正常運作的 Kubernetes 叢集，並已部署範例 Azure 投票應用程式。 在本教學課程中 (七個章節的第五部分)，您會將應用程式中的 Pod 擴增，然後嘗試進行 Pod 自動調整。 您也會了解如何調整 Azure VM 節點的數目，以變更叢集裝載工作負載的容量。 您會了解如何：

> [!div class="checklist"]
> * 調整 Kubernetes 節點
> * 手動調整執行應用程式的 Kubernetes Pod
> * 設定執行應用程式前端的自動調整 Pod

在其他教學課程中，Azure 投票應用程式會更新為新版本。

## <a name="before-you-begin"></a>開始之前

在先前的教學課程中，已將應用程式封裝為容器映像。 此映像已上傳至 Azure Container Registry，而您已建立 AKS 叢集。 接著已將應用程式部署至 AKS 叢集。 如果您尚未完成這些步驟，而且想要跟著做，請從[教學課程 1 – 建立容器映像][aks-tutorial-prepare-app]開始。

在本教學課程中，您必須執行 Azure CLI 2.0.53 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="manually-scale-pods"></a>手動調整 Pod

在先前的教學課程中部署 Azure Vote 前端與 Redis 執行個體時，已建立單一複本。 若要查看叢集中的 Pod 數目和狀態，請使用 [kubectl get][kubectl-get] 命令，如下所示：

```console
kubectl get pods
```

下列範例輸出會顯示一個前端 Pod 和一個後端 Pod：

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

若要手動變更 *azure-vote-front* 部署中的 Pod 數目，請使用 [kubectl scale][kubectl-scale] 命令。 下列範例會將前端 Pod 的數目增加為 *5*：

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

再次執行 [kubectl get pods][kubectl-get]，以確認 AKS 是否建立其他 Pod。 大約一分鐘後，其他 Pod 就會成為叢集中的可用項目：

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>自動調整 Pod

Kubernetes 支援[水平 Pod 自動調整][kubernetes-hpa]，可根據 CPU 使用率或其他選取的計量來調整部署中的 Pod 數目。 [計量伺服器][metrics-server]可用來將資源使用率提供給 Kubernetes，並且會自動部署在 AKS 叢集 1.10 版和更新版本中。 若要查看您的 AKS 叢集版本，請使用 [az aks show][az-aks-show] 命令，如下列範例所示：

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> 如果您的 AKS 叢集低於 1.10  ，則不會自動安裝計量伺服器。 計量伺服器安裝資訊清單可作為計量伺服器版本上的 `components.yaml` 資產，這表示您可以透過 URL 進行安裝。 若要深入了解這些 YAML 定義，請參閱讀我檔案的＜[部署][metrics-server-github]＞一節。
> 
> 安裝範例：
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

若要使用自動調整程式，必須為您 Pod 中所有容器和 Pod 定義 CPU 要求和限制。 在 `azure-vote-front` 部署中，前端容器已經要求 0.25 個 CPU，限制為 0.5 個 CPU。 這些資源要求和限制定義如下列範例程式碼片段所示：

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

下列範例會使用 [kubectl autoscale][kubectl-autoscale] 命令自動調整 *azure-vote-front* 部署中的 Pod 數目。 如果所有 Pod 的平均 CPU 使用率超過其所要求使用量的 50%，則自動調整程式會增加 Pod，最多可達 10  個執行個體。 然後，為此部署定義最少 3  個執行個體：

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

或者，您可以建立資訊清單檔，以定義自動調整程式行為和資源限制。 以下是資訊清單檔 (名稱為 `azure-vote-hpa.yaml`) 的範例。

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

使用 `kubectl apply` 來套用 `azure-vote-hpa.yaml` 資訊清單檔中定義的自動調整程式。

```
kubectl apply -f azure-vote-hpa.yaml
```

若要查看自動調整程式的狀態，請使用 `kubectl get hpa` 命令，如下所示：

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

幾分鐘之後，如果 Azure Vote 應用程式上的負載降到最低，Pod 複本數目就會自動降低成三個。 您可以再次使用 `kubectl get pods`，以查看移除的非必要 Pod。

## <a name="manually-scale-aks-nodes"></a>手動調整 AKS 節點

如果您在上一個教學課程中使用命令建立了 Kubernetes 叢集，它會有兩個節點。 如果您打算在叢集上增加或減少容器工作負載，可以手動調整節點的數目。

下列範例會在名為 myAKSCluster  的 Kubernetes 叢集中，將節點的數目增加到三個。 此命令需要幾分鐘的時間來完成。

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

成功調整叢集後，輸出會類似下列範例：

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 Kubernetes 叢集中使用不同的調整功能。 您已了解如何︰

> [!div class="checklist"]
> * 手動調整執行應用程式的 Kubernetes Pod
> * 設定執行應用程式前端的自動調整 Pod
> * 手動調整 Kubernetes 節點

請繼續進行下一個教學課程，以了解如何更新 Kubernetes 中的應用程式。

> [!div class="nextstepaction"]
> [更新 Kubernetes 中的應用程式][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
