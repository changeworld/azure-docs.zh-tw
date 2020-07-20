---
title: 快速入門：部署 Azure Kubernetes Service 叢集
description: 了解如何使用 PowerShell 快速建立 Kubernetes 叢集、部署應用程式，以及監視 Azure Kubernetes Service (AKS) 中的效能。
services: container-service
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: e786b64554b5fbaf5bb7051e09daca8fb1eaf049
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251411"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>快速入門：使用 PowerShell 部署 Azure Kubernetes Service 叢集

在此快速入門中，您將使用 PowerShell 部署 Azure Kubernetes Service (AKS) 叢集。 AKS 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在叢集上執行包含 Web 前端和 Redis 執行個體的多容器應用程式。 然後，您會了解如何監視叢集的健康情況和執行您應用程式的 Pod。

若要深入了解如何建立 Windows Server 節點集區，請參閱[建立支援 Windows Server 容器的 AKS 叢集][windows-container-powershell]。

![Azure Kubernetes Service 中部署的投票應用程式](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

本快速入門假設您已有 Kubernetes 概念的基本知識。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell][install-azure-powershell]。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶識別碼。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>建立資源群組

[Azure 資源群組](../azure-resource-manager/management/overview.md)是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [New-AzResourceGroup][new-azresourcegroup] Cmdelt 建立新的資源群組。

下列範例會在 eastus 地區建立名為 myResourceGroup 的資源群組。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

下列範例輸出顯示已成功建立的資源群組：

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>建立 AKS 叢集

使用 `ssh-keygen` 命令列公用程式產生 SSH 金鑰組。 如需詳細資料，請參閱[快速步驟：在 Azure 中建立及使用 Linux VM 的 SSH 公開和私密金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md)。

使用 [New-AzAks][new-azaks] Cmdlet 來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster 並包含一個節點的叢集。 預設也會啟用容器的 Azure 監視器。 這需要幾分鐘才能完成。

> [!NOTE]
> 建立 AKS 叢集時，系統會自動建立第二個資源群組來儲存 AKS 資源。 如需詳細資訊，請參閱[為何會使用 AKS 建立兩個資源群組？](./faq.md#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

在幾分鐘之後，此命令就會完成，並傳回叢集的相關資訊。

## <a name="connect-to-the-cluster"></a>連線至叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 `Install-AzAksKubectl` Cmdlet：

```azurepowershell
Install-AzAksKubectl
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [Import-AzAksCredential][import-azakscredential] Cmdlet。 下列範例會下載認證並設定 Kubernetes CLI 以供使用。

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurepowershell-interactive
.\kubectl get nodes
```

下列輸出範例會顯示上一個步驟中建立的單一節點。 請確定節點的狀態為 **Ready**：

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本教學課程中，資訊清單可用來建立執行 Azure 投票應用程式所需的所有物件。 此資訊清單包含兩個 [Kubernetes 部署][kubernetes-deployment]：一個適用於範例 Azure 投票 Python 應用程式，而另一個適用於 Redis 執行個體。 還會建立兩個 [Kubernetes 服務]：內部服務用於 Redis 執行個體，而外部服務用於從網際網路存取 Azure 投票應用程式。

> [!TIP]
> 在本快速入門中，您會以手動方式建立應用程式資訊清單，並將其部署至 AKS 叢集。
> 在更貼近現實的案例中，您可以使用 [Azure Dev Spaces][azure-dev-spaces] 快速地逐一查看程式碼，並直接在 AKS 叢集中進行偵錯。 您可以跨作業系統平台和開發環境來使用 Dev Spaces，並與小組中的其他人一起工作。

建立名為 `azure-vote.yaml` 的檔案，然後將下列 YAML 定義複製進來。 如果您使用 Azure Cloud Shell，可以使用 `vi` 或 `nano` 建立這個檔案，猶如使用虛擬或實體系統：

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

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

下列範例輸出會顯示已成功建立的部署和服務：

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。
此程序需要數分鐘的時間完成。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

一開始，**azure-vote-front** 服務的 **EXTERNAL-IP** 會顯示為 **pending**。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 **EXTERNAL-IP** 位址從 **pending** 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看 Azure 投票應用程式的實際運作情況，請開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址。

![Azure Kubernetes Service 中部署的投票應用程式](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

建立 AKS 叢集時，會啟用[適用於容器的 Azure 監視器](../azure-monitor/insights/container-insights-overview.md)來擷取叢集節點和 pod 的健康狀態計量。 在 Azure 入口網站中可取得這些健康狀態度量。

## <a name="delete-the-cluster"></a>選取叢集

若要避免 Azure 費用，您應該清除不需要的資源。 若不再需要叢集，可使用 [Remove-AzResourceGroup][remove-azresourcegroup] Cmdlet 來移除資源群組、容器服務和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="get-the-code"></a>取得程式碼

在本快速入門中，預先建立的容器映像已用來建立 Kubernetes 部署。 相關的應用程式程式碼、Dockerfile 和 Kubernetes 資訊清單檔案，都可以在 GitHub 上取得。

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已部署 Kubernetes 叢集，並將多容器應用程式部署到此叢集。 您也可以[存取 Kubernetes Web 儀表板][kubernetes-dashboard]，以供您的 AKS 叢集使用。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
