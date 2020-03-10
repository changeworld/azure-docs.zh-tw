---
title: 快速入門 - 在入口網站中建立 Azure Kubernetes 服務 (AKS) 叢集
description: 了解如何使用 Azure 入口網站快速建立 Kubernetes 叢集、部署應用程式，以及監視 Azure Kubernetes Service (AKS) 的效能。
services: container-service
ms.topic: quickstart
ms.date: 01/21/2020
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: f4885bea686267ce0397e9ca6f3e2c0ac8640971
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390264"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>快速入門：使用 Azure 入口網站部署 Azure Kubernetes Service （AKS）叢集

Azure Kubernetes Service (AKS) 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在本快速入門中，您會使用 Azure 入口網站來部署 AKS 叢集。 在叢集上執行包含 Web 前端和 Redis 執行個體的多容器應用程式。 然後，您會了解如何監視叢集的健康情況和執行您應用程式的 Pod。

![瀏覽至 Azure 投票應用程式範例的影像](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

本快速入門假設您已有 Kubernetes 概念的基本知識。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

若要建立 AKS 叢集，請完成下列步驟：

1. 從 Azure 入口網站功能表或 **[首頁]** 頁面，選取 [建立資源]。

2. 選取 [容器] >  [Kubernetes Service]。

3. 在 [基本資料] 頁面上設定下列選項：
    - **專案詳細資料**：選取 azure**訂**用帳戶，然後選取或建立 azure**資源群組**，例如*myResourceGroup*。
    - 叢集**詳細資料**：輸入**Kubernetes 叢集名稱**，例如*myAKSCluster*。 選取 AKS 叢集的 [區域]、[Kubernetes 版本] 及 [DNS 名稱前置詞]。
    - **主要節點集**區：選取 AKS 節點的 VM**節點大小**。 VM 大小「無法」在 AKS 叢集部署完畢後變更。 
            - 選取要部署到叢集的節點數目。 本快速入門將 [節點計數] 設為「1」。 節點計數*可以*在叢集部署完畢後調整。
    
    ![建立 AKS 叢集 - 提供基本資訊](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    選取 **[下一步]：** 完成時調整。

4. 在 [調整] 頁面上，保留預設選項。 在畫面底部，按 **[下一步：驗證]** 。
    > [!CAUTION]
    > 建立新的 AAD 服務主體可能需要數分鐘的時間才能傳播並變成可用，導致 Azure 入口網站中找不到服務主體的錯誤和驗證失敗。 如果您遇到這種情況，請造訪[這裡](troubleshooting.md#im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one)以取得緩和措施。

5. 在 [驗證] 頁面上，設定下列選項：
    - 讓 [服務主體] 欄位保留 **(新的) 預設服務主體**，以建立新的服務主體。 您也可以選擇 [設定服務主體] 以使用現有服務主體。 如果您使用現有服務主體，您必須提供 SPN 用戶端識別碼和祕密。
    - 啟用 Kubernetes 角色型存取控制 (RBAC) 的選項。 這會針對 AKS 叢集中部署的 Kubernetes 資源提供更細部的存取控管。

根據預設，會使用「基本」網路，並啟用適用於容器的 Azure 監視器。 按一下 [檢閱 + 建立]，然後在驗證完成時按一下 [建立]。

建立 AKS 叢集需要幾分鐘的時間。 完成部署時，請按一下 [移至資源]，或瀏覽至 AKS 叢集資源群組 (例如 myResourceGroup)，然後選取 AKS 資源 (例如 myAKSCluster)。 AKS 叢集儀表板隨即顯示，如此範例所示：

![Azure 入口網站中的 AKS 儀表板範例](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>連線至叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 `kubectl` 用戶端會預先安裝在 Azure Cloud Shell 中。

使用 Azure 入口網站頂端的 `>_` 按鈕開啟 Cloud Shell。

![在入口網站中開啟 Azure Cloud Shell](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。 下列範例會針對 myResourceGroup 資源群組中的叢集名稱 myAKSCluster 取得認證：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
kubectl get nodes
```

下列輸出範例會顯示上一個步驟中建立的單一節點。 請確定節點的狀態為 *Ready*：

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本教學課程中，資訊清單可用來建立執行 Azure 投票應用程式所需的所有物件。 此資訊清單包含兩個 [Kubernetes 部署][kubernetes-deployment]：一個適用於範例 Azure 投票 Python 應用程式，而另一個適用於 Redis 執行個體。 還會建立兩個 [Kubernetes 服務][kubernetes-service]：內部服務用於 Redis 執行個體，而外部服務用於從網際網路存取 Azure 投票應用程式。

> [!TIP]
> 在本快速入門中，您會以手動方式建立應用程式資訊清單，並將其部署至 AKS 叢集。 在更貼近現實的案例中，您可以使用 [Azure Dev Spaces][azure-dev-spaces] 快速地逐一查看程式碼，並直接在 AKS 叢集中進行偵錯。 您可以跨作業系統平台和開發環境來使用 Dev Spaces，並與小組中的其他人一起工作。

在 Cloud Shell 中，使用 [`nano azure-vote.yaml`] 或 [`vi azure-vote.yaml`] 命令來建立名為 `azure-vote.yaml`的檔案。 然後，將下列 YAML 定義複製到其中：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

使用 [kubectl apply][kubectl-apply] 命令來部署應用程式並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f azure-vote.yaml
```

下列範例輸出會顯示已成功建立的部署和服務：

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```console
kubectl get service azure-vote-front --watch
```

一開始，*azure-vote-front* 服務的 *EXTERNAL-IP* 會顯示為 *pending*。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看 Azure 投票應用程式的實際運作情況，請開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址。

![瀏覽至 Azure 投票應用程式範例的影像](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>監視健康情況和記錄

當您建立叢集後，就已啟用容器的 Azure 監視器。 此監視功能可針對 AKS 叢集和叢集上執行的 Pod 提供健康情況計量。

可能需要幾分鐘，此資料才會填入至 Azure 入口網站。 若要查看 Azure 投票應用程式 Pod 的目前狀態、執行時間及資源使用量，請往回瀏覽到 Azure 入口網站中的 AKS 資源，例如 myAKSCluster。 然後，您可以存取健康狀態，如下所示：

1. 在左側的 [監視] 底下，選擇 [見解]
1. 在頂端選擇 [+ 新增篩選器]
1. 選取 [命名空間] 作為屬性，然後選擇 *除了 kube-system 以外的所有項目\<\>*
1. 選擇檢視**容器**。

系統會顯示 azure-vote-back 和 azure-vote-front 容器，如下列範例所示：

![檢視 AKS 中執行的容器健康情況](media/kubernetes-walkthrough-portal/monitor-containers.png)

若要查看 `azure-vote-front` Pod 的記錄，請從容器清單的下拉式清單中選取 [檢視容器記錄]。 這些記錄包含來自容器的 stdout 和 stderr 資料流。

![在 AKS 中檢視容器記錄](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>刪除叢集

不再需要叢集時，請刪除叢集資源，這將會刪除所有相關的資源。 此操作可以在 Azure 入口網站中選取 AKS 叢集儀表板上的 [刪除] 按鈕來完成。 或者，您可以在 Cloud Shell 中使用 [az aks delete][az-aks-delete] 命令：

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。

## <a name="get-the-code"></a>取得程式碼

在本快速入門中，預先建立的容器映像已用來建立 Kubernetes 部署。 相關的應用程式程式碼、Dockerfile 和 Kubernetes 資訊清單檔案，都可以在 GitHub 上取得。

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Kubernetes 叢集，並將多容器應用程式部署到此叢集。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../monitoring/monitoring-container-health.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
