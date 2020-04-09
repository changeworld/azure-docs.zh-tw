---
title: 在 Azure 庫伯內斯服務群集中執行 Windows 伺服器容器
description: 瞭解如何快速創建 Kubernetes 群集,使用 Azure CLI 在 Azure Kubernetes 服務 (AKS) 中的 Windows Server 容器中部署應用程式。
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 8d2a91f63815e7ba4bcbe4084b80a06fa7779099
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886716"
---
# <a name="preview---create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>預覽 - 使用 Azure CLI 在 Azure 庫伯奈斯服務 (AKS) 叢集上建立 Windows 伺服器容器

Azure Kubernetes Service (AKS) 是受控 Kubernetes 服務，可讓您快速部署及管理叢集。 在本文中,使用 Azure CLI 部署 AKS 群集。 還將 Windows Server 容器中的範例應用程式ASP.NET部署到叢集。

此功能目前為預覽狀態。

![瀏覽到ASP.NET範例應用程式的影像](media/windows-container/asp-net-sample-app.png)

本文假定對庫伯內斯概念的基本理解。 如需詳細資訊，請參閱 [Azure Kubernetes Services (AKS) 的 Kubernetes 核心概念][kubernetes-concepts]。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果選擇在本地安裝和使用 CLI,則本文要求您運行 Azure CLI 版本 2.0.61 或更高版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="before-you-begin"></a>開始之前

創建可以運行 Windows Server 容器的群集後,必須添加其他節點池。 稍後的步驟將介紹添加其他節點池,但首先需要啟用一些預覽功能。

> [!IMPORTANT]
> AKS 預覽功能是自助服務加入宣告。 預覽版提供「根據」和「可用」,不在服務層級協定和有限保修中。 在盡力的基礎上,客戶支援部分覆蓋了 AKS 預覽。 因此,這些功能不適合生產用途。 有關其他操作,請參閱以下支援文章:
>
> * [AKS 支援原則][aks-support-policies]
> * [Azure 支援常見問題集][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>安裝 aks-preview CLI 擴充功能

要使用 Windows Server 容器,您需要*aks 預覽*CLI 擴充版本 0.4.12 或更高版本。 使用[az 延伸新增][az-extension-add]指令安裝*aks 預覽*Azure CLI 擴充,然後使用[az 延伸更新][az-extension-update]指令檢查任何可用更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-windows-preview-feature"></a>註冊 Windows 預覽功能

要建立可以使用多個節點池並運行 Windows Server 容器的 AKS 叢集,請先在訂閱上啟用*Windows 預覽*功能標誌。 *Windows 預覽*功能還使用多節點池群集和虛擬機器擴展集來管理 Kubernetes 節點的部署和配置。 使用[az 要素寄存器][az-feature-register]命令註冊*Windows 預覽*功能標誌,如以下範例所示:

```azurecli-interactive
az feature register --name WindowsPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 成功註冊*Windows 預覽*功能標誌後創建的任何 AKS 群集都使用此預覽群集體驗。 要繼續創建常規的、完全支援的群集,請不要在生產訂閱上啟用預覽功能。 使用單獨的測試或開發 Azure 訂閱來測試預覽功能。

完成註冊需要幾分鐘時間。 使用[az 元素清單][az-feature-list]命令檢查註冊狀態:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/WindowsPreview')].{Name:name,State:properties.state}"
```

當註冊狀態為`Registered`時,按 Ctrl-C 停止監視狀態。  然後使用[az 提供程式寄存器][az-provider-register]指令刷新*Microsoft.ContainerService*資源提供程式的註冊:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>限制

建立與管理支援多個節點池的 AKS 叢集時,以下限制適用:

* 無法刪除第一個節點池。

當此功能處於預覽版時,以下附加限制適用:

* AKS 群集最多可以有八個節點池。
* AKS 群集在這八個節點池中最多可以有 400 個節點。
* Windows Server 節點池名稱的限制為6個字元。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 建立資源群組時，系統會要求您指定位置。 此位置是儲存資源群組中繼資料的位置，如果您未在資源建立期間指定另一個區域，此位置也會是您在 Azure 中執行資源的位置。 使用 [az group create][az-group-create] 命令來建立資源群組。

下面的範例在*東部*位置創建名為*myResourceGroup*的資源組。

> [!NOTE]
> 本文在本教程中對命令使用 Bash 語法。
> 如果使用 Azure 雲外殼,請確保雲殼視窗左上角的下拉設定為**Bash**。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

下列範例輸出顯示已成功建立的資源群組：

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

為了運行支援 Windows Server 容器節點池的 AKS 群集,叢集需要使用使用[Azure CNI(][azure-cni-about]進階)網路外掛程式的網路策略。 有關幫助規劃所需子網範圍和網路注意事項的更多詳細資訊,請參閱[配置 Azure CNI 網路][use-advanced-networking]。 使用[az aks 建立][az-aks-create]指令建立名為*myAKSCluster*的 AKS 群集。 如果不存在,此命令將創建必要的網路資源。
  * 叢集設定了兩個節點
  * *Windows-admin 密碼*和*windows 管理員使用者名*參數為群集上創建的任何 Windows Server 容器設置管理員認證。

> [!NOTE]
> 為確保群集可靠運行,應在默認節點池中至少運行 2 個節點。

提供您自己的安全*PASSWORD_WIN(* 請記住,本文中的指令已輸入 BASH 外殼):

```azurecli-interactive
PASSWORD_WIN="P@ssw0rd1234"

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --kubernetes-version 1.15.7 \
    --generate-ssh-keys \
    --windows-admin-password $PASSWORD_WIN \
    --windows-admin-username azureuser \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --network-plugin azure
```

> [!Note]
> 如果收到密碼驗證錯誤,請嘗試在另一個區域中創建資源組。
> 然後嘗試使用新資源組創建群集。

> [!Note]
> 如果由於該區域不支援該版本而無法建立 AKS 群集,則可以使用 [az aks get-version -- 位置 Eastus] 命令查找此區域的支援版本清單。


在幾分鐘之後，此命令就會完成，並以 JSON 格式傳回叢集的相關資訊。 有時,群集可能需要超過幾分鐘的時間進行預配。 在這些情況下,最多等待 10 分鐘。 

## <a name="add-a-windows-server-node-pool"></a>新增 Windows 伺服器節點池

默認情況下,使用可以運行 Linux 容器的節點池創建 AKS 群集。 使用`az aks nodepool add`命令添加可運行 Windows Server 容器的其他節點池。

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1 \
    --kubernetes-version 1.15.7
```

上述指令建立名為*npwin*的新節點池,並將其新增到*myAKSCluster*。 建立節點池以執行 Windows Server 容器時,節點*vm 大小的*預設值為*Standard_D2s_v3*。 如果選擇設定*節點 vm 大小*參數,請檢查受限 VM[大小][restricted-vm-sizes]的清單。 建議的最小大小為*Standard_D2s_v3。* 上述命令還使用運行`az aks create`時創建的預設 vnet 中的預設子網。

## <a name="connect-to-the-cluster"></a>連線至叢集

若要管理 Kubernetes 叢集，請使用 Kubernetes 命令列用戶端：[kubectl][kubectl]。 如果您使用 Azure Cloud Shell，則 `kubectl` 已安裝。 若要在本機安裝 `kubectl`，請使用 [az aks install-cli][az-aks-install-cli] 命令：

```azurecli
az aks install-cli
```

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
kubectl get nodes
```

以下範例輸出顯示群集中的所有節點。 確保所有節點的狀態*為:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.15.7
aksnpwin987654                      Ready    agent   108s   v1.15.7
```

## <a name="run-the-application"></a>執行應用程式

Kubernetes 資訊清單檔會定義所需的叢集狀態，例如要執行哪些容器映像。 在本文中,清單用於創建在 Windows Server 容器中運行 ASP.NET 示例應用程式所需的所有物件。 此清單包括用於 ASP.NET 範例應用程式的[Kubernetes 部署][kubernetes-deployment],以及用於從 Internet 存取應用程式的外部[Kubernetes 服務][kubernetes-service]。

ASP.NET示例應用程式作為[.NET 框架範例][dotnet-samples]的一部分提供,並在 Windows Server 容器中運行。 AKS 要求 Windows 伺服器容器基於*Windows 伺服器 2019*或更高版本的圖像。 Kubernetes 清單檔還必須定義一個[節點選擇器][node-selector],以告訴 AKS 叢集在可以執行 Windows Server 容器的節點上執行ASP.NET示例應用程式的 pod。

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

```console
kubectl apply -f sample.yaml
```

以下範例輸出顯示已成功建立的部署和服務:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>測試應用程式

執行應用程式時，Kubernetes 服務會向網際網路公開前端應用程式。 此程序需要數分鐘的時間完成。 有時,服務可能需要超過幾分鐘的時間進行預配。 在這些情況下,最多等待 10 分鐘。

若要監視進度，請使用 [kubectl get service][kubectl-get] 命令搭配 `--watch` 引數。

```console
kubectl get service sample --watch
```

最初,*範例*服務的*外外部 IP*顯示為*掛起*。

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

當 *EXTERNAL-IP* 位址從 *pending* 變成實際的公用 IP 位址時，請使用 `CTRL-C` 停止 `kubectl` 監看式流程。 下列範例輸出會顯示已指派給服務的有效公用 IP 位址：

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

要查看範例應用程式,請打開 Web 瀏覽器到服務的外部 IP 位址。

![瀏覽到ASP.NET範例應用程式的影像](media/windows-container/asp-net-sample-app.png)

> [!Note]
> 如果在嘗試載入頁面時收到連接超時,則應使用以下命令 [kubectl get pod -- watch] 驗證範例應用是否已準備就緒。 有時,在外部 IP 位址可用時,不會啟動視窗容器。

## <a name="delete-cluster"></a>刪除叢集

若不再需要叢集，可使用 [az group delete][az-group-delete] 命令來移除資源群組、容器服務和所有相關資源。

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> 當您刪除叢集時，不會移除 AKS 叢集所使用的 Azure Active Directory 服務主體。 如需有關如何移除服務主體的步驟，請參閱[AKS 服務主體的考量和刪除][sp-delete]。

## <a name="next-steps"></a>後續步驟

在本文中,您部署了 Kubernetes 群集,並在 Windows Server 容器中向其部署了ASP.NET示例應用程式。 [存取 Kubernetes Web 儀表板][kubernetes-dashboard]，以使用您剛才建立的叢集。

若要深入了解 AKS，並逐步完成部署範例的完整程式碼，請繼續 Kubernetes 叢集教學課程。

> [!div class="nextstepaction"]
> [AKS 教程][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: https://aka.ms/coingfonboarding
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-advanced-networking.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
