---
title: 使用 Azure CLI 建立虛擬節點
titleSuffix: Azure Kubernetes Service
description: 了解如何使用 Azure CLI 建立使用虛擬節點執行 Pod 的 Azure Kubernetes Service (AKS) 叢集。
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: a655c8c145b4f3812dae9f1a4ec1e5eebbe44809
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348469"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>使用 Azure CLI 建立和設定 Azure Kubernetes Service (AKS) 叢集以使用虛擬節點

本文說明如何使用 Azure CLI 來建立和設定虛擬網路資源和 AKS 叢集，然後啟用虛擬節點。

> [!NOTE]
> [本文提供使用](virtual-nodes.md) 虛擬節點的區域可用性和限制總覽。

## <a name="before-you-begin"></a>開始之前

虛擬節點能在於 Azure 容器執行個體 (ACI) 與 AKS 叢集中執行的 Pod 之間啟用網路通訊。 為了提供此通訊功能，需要建立虛擬網路子網路並指派委派權限。 虛擬節點只適用于使用 *advanced* 網路 (Azure CNI) 所建立的 AKS 叢集。 根據預設，系統會使用 *基本* 網路 (kubenet) 來建立 AKS 叢集。 本文說明如何建立虛擬網路和子網路，然後部署使用進階網路的 AKS 叢集。

如果您先前未使用 ACI，請向您的訂用帳戶註冊服務提供者。 您可以使用 [az provider list][az-provider-list] 命令來檢查 ACI 提供者註冊狀態，如下列範例所示：

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

*Microsoft.ContainerInstance* 提供者應該回報為 *Registered* ，如以下範例輸出所示：

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

如果提供者顯示為 *NotRegistered* ，請使用 [az provider register][az-provider-register] 來註冊提供者，如下列範例所示：

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="launch-azure-cloud-shell"></a>啟動 Azure Cloud Shell

Azure Cloud Shell 是免費的互動式 Shell，可讓您用來執行本文中的步驟。 它具有預先安裝和設定的共用 Azure 工具，可與您的帳戶搭配使用。

若要開啟 Cloud Shell，請選取程式碼區塊右上角的 [試試看]。 您也可以移至 [https://shell.azure.com/bash](https://shell.azure.com/bash)，從另一個瀏覽器索引標籤啟動 Cloud Shell。 選取 [複製]  即可複製程式碼區塊，將它貼到 Cloud Shell 中，然後按 enter 鍵加以執行。

如果您偏好在本機安裝和使用 CLI，本文需要有 Azure CLI 2.0.49 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI]( /cli/azure/install-azure-cli)。

## <a name="create-a-resource-group"></a>建立資源群組

Azure 資源群組是部署及管理 Azure 資源所在的邏輯群組。 使用 [az group create][az-group-create] 命令來建立資源群組。 下列範例會在 *westus* 位置建立名為 *myResourceGroup* 的資源群組。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>建立虛擬網路

使用 [az network vnet create][az-network-vnet-create] 命令來建立虛擬網路。 下列範例會建立名為 myVnet 的虛擬網路 (位址首碼為 10.0.0.0/8)，以及名為 myAKSSubnet 的子網路。 這個子網路的位址首碼預設為 10.240.0.0/16：

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

現在，使用 [az network vnet subnet create][az-network-vnet-subnet-create] 命令為虛擬節點建立其他子網路。 下列範例會建立名為 myVirtualNodeSubnet 且位址首碼為 10.241.0.0/16 的子網路。

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal-or-use-a-managed-identity"></a>建立服務主體或使用受控識別

為了允許 AKS 叢集與其他 Azure 資源互動，則會使用 Azure Active Directory 服務主體。 此服務主體可由 Azure CLI 或入口網站自動建立，或者您可以預先建立一個並指派其他權限。 您也可以使用受控識別，而不是服務主體來取得權限。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。

使用 [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] 命令建立服務主體。 `--skip-assignment` 參數會限制指派任何其他權限。

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

輸出類似於下列範例：

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

記下 appId 和密碼。 下列步驟中會使用這些值。

## <a name="assign-permissions-to-the-virtual-network"></a>對虛擬網路指派權限

若要允許叢集使用及管理虛擬網路，您必須對 AKS 服務主體授與正確權限，使其可以使用網路資源。

首先，使用 [az network vnet show][az-network-vnet-show] 取得虛擬網路資源識別碼：

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

若要授與 AKS 叢集使用虛擬網路的正確存取權，請使用 [az role assignment create][az-role-assignment-create] 命令建立角色指派。 以在前兩個步驟中蒐集的值取代 `<appId` 和 `<vnetId>`。

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>建立 AKS 叢集

您必須將 AKS 叢集部署到上一個步驟所建立的 AKS 子網路。 請使用 [az network vnet subnet show][az-network-vnet-subnet-show] 取得這個子網路的識別碼：

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

使用 [az aks create][az-aks-create] 命令來建立 AKS 叢集。 下列範例會建立名為 myAKSCluster  並包含一個節點的叢集。 取代為 `<subnetId>` 在上一個步驟中取得的識別碼，並 `<appId>` 以 `<password>` 上一節中收集的值取代。

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

在數分鐘之後，該命令會完成並傳回關於節點的 JSON 格式資料。

## <a name="enable-virtual-nodes-addon"></a>啟用虛擬節點附加元件

若要啟用虛擬節點，請立即使用 [az aks enable-addons][az-aks-enable-addons] 命令。 下列範例會使用上一個步驟所建立的子網路，其名稱為 myVirtualNodeSubnet：

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>連線至叢集

若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此步驟會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
kubectl get nodes
```

下列輸出範例顯示單一 VM 節點已建立，然後是適用於 Linux 的虛擬節點 (virtual-node-aci-linux)：

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>部署範例應用程式

建立名為 `virtual-node.yaml` 的檔案，然後將下列 YAML 複製進來。 若要在節點上對容器進行排程，需定義 [nodeSelector][node-selector] 和 [toleration][toleration]。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: mcr.microsoft.com/azuredocs/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

使用 [kubectl apply][kubectl-apply] 命令來執行應用程式。

```console
kubectl apply -f virtual-node.yaml
```

使用 [kubectl get pods][kubectl-get] 命令搭配 `-o wide` 引數來輸出 Pod 清單和已排程的節點。 請注意，`aci-helloworld` pod 已排定在 `virtual-node-aci-linux` 節點上。

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

Pod 會從 Azure 虛擬網路的子網路 (為搭配使用虛擬節點而委派) 獲派內部 IP 位址。

> [!NOTE]
> 如果您使用儲存在 Azure Container Registry 中的映像，請[設定並使用 Kubernetes 祕密][acr-aks-secrets]。 虛擬節點目前的其中一個限制，是您無法使用整合式 Azure AD 服務主體驗證。 如果您未使用祕密，已在虛擬節點上排程的 Pod 就無法啟動並會回報錯誤 `HTTP response status code 400 error code "InaccessibleImage"`。

## <a name="test-the-virtual-node-pod"></a>測試虛擬節點 Pod

若要測試虛擬節點上執行的 Pod，請瀏覽至搭配 Web 用戶端的示範應用程式。 由於 Pod 會獲派內部 IP 位址，因此您可以快速地從 AKS 叢集上其他 Pod 測試此連線。 建立測試 Pod，並將終端機工作階段與它連結：

```console
kubectl run -it --rm testvk --image=debian
```

使用 `apt-get` 在 Pod 中安裝 `curl`：

```console
apt-get update && apt-get install -y curl
```

現在您可以使用 `curl` (例如 http://10.241.0.4) 來存取 Pod 的位址。 提供前述 `kubectl get pods` 命令中您自己的 IP 位址：

```console
curl -L http://10.241.0.4
```

示範應用程式會隨即顯示，如下列簡要範例輸出所示：

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

使用 `exit` 來關閉測試 Pod 的終端機工作階段。 當您的工作階段結束時，Pod 也會一併刪除。

## <a name="remove-virtual-nodes"></a>移除虛擬節點

如果您不想再使用虛擬節點，則可以使用 [az aks disable-addons][az aks disable-addons] 命令來予以停用。 

如有必要，請移至， [https://shell.azure.com](https://shell.azure.com) 以在瀏覽器中開啟 Azure Cloud Shell。

首先，刪除 `aci-helloworld` 虛擬節點上執行的 pod：

```console
kubectl delete -f virtual-node.yaml
```

下列範例命令會停用 Linux 虛擬節點：

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

現在，移除虛擬網路資源和資源群組：

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>後續步驟

在本文中，Pod 已在虛擬節點上進行排程，並獲派私人的內部 IP 位址。 您可以改為建立服務部署，並透過負載平衡器或輸入控制器將流量路由到您的 Pod。 如需詳細資訊，請參閱[在 AKS 中建立基本的連入控制器][aks-basic-ingress]。

虛擬節點往往是 AKS 中調整解決方案的一個元件。 如需有關調整解決方案的詳細資訊，請參閱下列文章：

- [使用 Kubernetes 水平 Pod 自動調整器][aks-hpa]
- [使用 Kubernetes 叢集自動調整器][aks-cluster-autoscaler]
- [查看適用於虛擬節點的自動調整範例][virtual-node-autoscale]
- [深入了解 Virtual Kubelet 開放原始碼程式庫][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
