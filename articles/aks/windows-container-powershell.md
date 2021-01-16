---
title: 使用 PowerShell 在 AKS 叢集中建立 Windows Server 容器
description: 瞭解如何使用 PowerShell 快速建立 Kubernetes 叢集、在 Azure Kubernetes Service (AKS) 的 Windows Server 容器中部署應用程式。
services: container-service
ms.topic: article
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 97741423fa8b689a92bd9db78b810e6b86aefcbd
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247058"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>使用 PowerShell 在 Azure Kubernetes Service (AKS) 叢集上建立 Windows Server 容器

Azure Kubernetes Service (AKS) 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在本文中，您會使用 PowerShell 來部署 AKS 叢集。 您也可以將 Windows Server 容器中的 `ASP.NET` 範例應用程式部署到叢集。

![瀏覽至 ASP.NET 應用程式範例的影像](media/windows-container-powershell/asp-net-sample-app.png)

本文章假設您對 Kubernetes 概念有基本瞭解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

## <a name="prerequisites"></a>Prerequisites

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

如果您選擇在本機使用 PowerShell，本文會要求您安裝 Az PowerShell 模組，並使用 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) Cmdlet 連線到您的 Azure 帳戶。 如需安裝 Az PowerShell 模組的詳細資訊，請參閱[安裝 Azure PowerShell][install-azure-powershell]。 您也必須安裝 Az. Aks PowerShell 模組： 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

如果您有多個 Azure 訂用帳戶，請選擇資源計費的適當訂用帳戶。 使用 [Set-AzContext](/powershell/module/az.accounts/set-azcontext) Cmdlet 來選取特定的訂用帳戶識別碼。

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>限制

在建立和管理支援多個節點集區的 AKS 叢集時，需遵守下列限制：

* 您無法刪除第一個節點集區。

下列其他限制適用於 Windows Server 節點集區：

* AKS 叢集最多可以有 10 個節點集區。
* 在每個節點集區中，AKS 叢集最多可以有 100 個節點。
* Windows Server 節點集區名稱的限制為 6 個字元。

## <a name="create-a-resource-group"></a>建立資源群組

[Azure 資源群組](../azure-resource-manager/management/overview.md)是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [New-AzResourceGroup][new-azresourcegroup] Cmdelt 建立新的資源群組。

下列範例會在 eastus 位置建立名為 myResourceGroup 的資源群組。

> [!NOTE]
> 本文會針對本教學課程中的命令使用 PowerShell 語法。 如果您使用 Azure Cloud Shell，請確定 [Cloud Shell] 視窗左上角的下拉式清單已設定為 [PowerShell]。

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

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

使用 `ssh-keygen` 命令列公用程式產生 SSH 金鑰組。 如需詳細資料，請參閱[快速步驟：在 Azure 中建立及使用 Linux VM 的 SSH 公開和私密金鑰組](../virtual-machines/linux/mac-create-ssh-keys.md)。

若要執行支援 Windows Server 容器之節點集區的 AKS 叢集，您的叢集必須採用使用 [Azure CNI][azure-cni-about] (advanced) 網路外掛程式的網路原則。 如需協助方案必要子網路範圍和網路考慮的詳細資訊，請參閱[設定 Azure CNI 網路][use-advanced-networking]。 使用下列的 [New - AzAks][new-azaks] Cmdlet 來建立名稱為 **myAKSCluster** 的 AKS 叢集。 下列範例會建立所需的網路資源 (如果不存在)。

> [!NOTE]
> 若要確保叢集能夠可靠地運作，您應該在預設節點集區中執行至少 2 個 (兩個) 節點。

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -KubernetesVersion 1.16.7 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName akswinuser -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> 如果您因為此區域不支援版本而無法建立 AKS 叢集，您可以使用 `Get-AzAksVersion -Location eastus` 命令來尋找此區域支援的版本清單。

在幾分鐘之後，此命令就會完成，並傳回叢集的相關資訊。 叢集有時可能需要超過幾分鐘的時間來佈建。 在這些情況下，最多允許 10 分鐘的時間。

## <a name="add-a-windows-server-node-pool"></a>新增 Windows Server 節點集區

根據預設，系統會使用可執行 Linux 容器的節點集區來建立 AKS 叢集。 使用 `New-AzAksNodePool` Cmdlet 來新增可在 Linux 節點集區旁執行 Windows Server 容器的節點集區。

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -OsType Windows -Name npwin -KubernetesVersion 1.16.7
```

上述命令會建立名為 **npwin** 的新節點集區，並將此新增至 **myAKSCluster**。 建立節點集區以執行 Windows Server 容器時，**VmSize** 的預設值是 **Standard_D2s_v3**。 如果您選擇設定 **VmSize** 參數，請檢查 [限制的 VM 大小][restricted-vm-sizes]清單。 最小的建議大小是 **Standard_D2s_v3**。 上一個命令也會使用執行 `New-AzAks` 時，預設 vnet 中所建立的預設子網路。

## <a name="connect-to-the-cluster"></a>連線至叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 `Install-AzAksKubectl` Cmdlet：

```azurepowershell-interactive
Install-AzAksKubectl
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [Import-AzAksCredential][import-azakscredential] Cmdlet。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```azurepowershell-interactive
kubectl get nodes
```

下列範例輸出顯示叢集中的所有節點。 請確定所有節點的狀態為 **就緒**：

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本文中，資訊清單可用來建立在 Windows Server 容器中執行 ASP.NET 範例應用程式所需的所有物件。 此資訊清單包含 ASP.NET 範例應用程式的 [Kubernetes 部署][kubernetes-deployment]，以及從網際網路存取應用程式的外部 [Kubernetes 服務][kubernetes-service]。

ASP.NET 範例應用程式是 [.NET Framework 範例][dotnet-samples]中提供的一部份，並且可在 Windows Server 容器中執行。 AKS 需要 Windows Server 容器根據 **Windows Server 2019** 或更新版本的映像。 Kubernetes 資訊清單檔案也必須定義[節點選取器][node-selector]以指示您的 AKS 叢集在可執行 Windows Server 容器的節點上執行 ASP.NET 範例應用程式的 Pod。

建立名為 `sample.yaml` 的檔案，然後將下列 YAML 定義複製進來。 如果您使用 Azure Cloud Shell，可以使用 `vi` 或 `nano` 建立這個檔案，猶如使用虛擬或實體系統：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

使用 [kubectl apply][kubectl-apply] 命令來部署應用程式並指定 YAML 資訊清單的名稱：

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

下列範例輸出會顯示已成功建立的部署和服務：

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。
此程序需要數分鐘的時間完成。 服務有時可能需要超過幾分鐘的時間來佈建。 在這些情況下，最多允許 10 分鐘的時間。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```azurepowershell-interactive
kubectl get service sample --watch
```

一開始，**sample** 服務的 **EXTERNAL-IP** 會顯示為 **pending**。

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 **EXTERNAL-IP** 位址從 **pending** 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

若要查看範例應用程式的實際運作情況，請開啟網頁瀏覽器並瀏覽至服務的外部 IP 位址。

![瀏覽至 ASP.NET 應用程式範例的影像](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> 如果您在嘗試載入頁面時收到連接逾時，您應該使用下列命令 `kubectl get pods --watch` 確認範例應用程式已就緒。 有時候，當您的外部 IP 位址可供使用時，Windows 容器將不會啟動。

## <a name="delete-cluster"></a>刪除叢集

若不再需要叢集，可使用 [Remove-AzResourceGroup][remove-azresourcegroup] Cmdlet 來移除資源群組、容器服務和所有相關資源。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱 [AKS 服務主體的考量和刪除][sp-delete]。 如果您使用受控識別，則身分識別會由平台負責管理，您不需要刪除。

## <a name="next-steps"></a>後續步驟

在本文中，您已部署 Kubernetes 叢集，並在 Windows Server 容器中將 `ASP.NET` 範例應用程式部署至該叢集。 [存取 Kubernetes Web 儀表板][kubernetes-dashboard]，以使用您已建立的叢集。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教學課程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
