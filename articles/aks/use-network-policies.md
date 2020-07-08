---
title: 透過網路原則保護 Pod 流量
titleSuffix: Azure Kubernetes Service
description: 了解如何在 Azure Kubernetes Service (AKS) 中使用 Kubernetes 網路原則，保護流入及流出的流量
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 7e494c6ac89289a9b271d16b871b8a22e1ca9e6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683201"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量

當您在 Kubernetes 中執行新式微服務架構的應用程式時，您通常想要控制哪些元件可以彼此通訊。 最低權限原則應該套用至流量可以在 Azure Kubernetes Service (AKS) 叢集中的 Pod 之間流動的方式。 假設您可能想要封鎖直接流向後端應用程式的流量。 Kubernetes 中的「網路原則」功能可讓您針對叢集中 Pod 之間的輸入和輸出流量定義規則。

本文示範如何安裝網路原則引擎，並建立 Kubernetes 網路原則來控制 AKS 中 Pod 之間的流量流程。 網路原則只應用於 AKS 中的 Linux 型節點和 Pod。

## <a name="before-you-begin"></a>開始之前

您必須安裝並設定 Azure CLI 版本 2.0.61 或更新版本。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

> [!TIP]
> 如果您已在預覽期間使用網路原則功能，我們建議您[建立新的叢集](#create-an-aks-cluster-and-enable-network-policy)。
> 
> 如果您想要繼續使用在預覽期間使用網路原則的現有測試叢集，請將您的叢集升級至最新 GA 版本的新 Kubernetes 版本，然後部署下列 YAML 資訊清單，以修正損毀的計量伺服器和 Kubernetes 儀表板。 只有使用 Calico 網路原則引擎的叢集才需要此修正。
>
> 作為安全性最佳做法，[檢閱此 YAML 資訊清單的內容，][calico-aks-cleanup]了解部署到 AKS 叢集的內容。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>網路原則概觀

根據預設，AKS 叢集中的所有 Pod 都可以無限制地傳送及接收流量。 為了提升安全性，您可以定義可控制流量流程的規則。 例如，後端應用程式通常只會對必要的前端服務公開。 或者，只有連線到資料庫元件的應用層才能存取它們。

網路原則是一種 Kubernetes 規格，其定義 Pod 之間通訊的存取原則。 使用網路原則時，您可以定義一組已排序的規則來傳送和接收流量，並將其套用至符合一或多個標籤選取器的 Pod 集合。

這些網路原則規則會定義為 YAML 資訊清單。 網路原則可納入更廣泛的資訊清單中，而該清單也會建立部署或服務。

### <a name="network-policy-options-in-aks"></a>AKS 中的網路原則選項

Azure 提供兩種方式來實作網路原則。 當您建立 AKS 叢集時，可以選擇網路原則選項。 建立叢集之後，就無法變更原則選項：

* Azure 本身的實作，稱為 *Azure 網路原則*。
* *Calico 網路原則*，這是 [Tigera][tigera] 創建的開放原始碼網路和網路安全性解決方案。

這兩個部署都會使用 Linux *IPTables* 來強制執行指定的原則。 這些原則會轉譯成一組允許和不允許的 IP 配對。 這些配對接著會透過程式設計為 IPTable 篩選規則。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 與 Calico 原則之間的差異及其功能

| 功能                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 支援的平台                      | Linux                      | Linux                       |
| 支援的網路功能選項             | Azure CNI                  | Azure CNI 和 kubenet       |
| 符合 Kubernetes 規格 | 支援所有原則類型 |  支援所有原則類型 |
| 其他功能                      | None                       | 由全域網路原則、全域網路集和主機端點組成的延伸原則模型。 如需使用 `calicoctl` CLI 管理這些擴充功能的詳細資訊，請參閱 [calicoctl 使用者參考][calicoctl]。 |
| 支援                                  | 受到 Azure 支援和工程團隊支援 | Azure 社群支援。 如需其他付費支援的詳細資訊，請參閱 [Project Calico 支援選項][calico-support]。 |
| 記錄                                  | 在 IPTables 中新增/刪除的規則會記錄在每一部主機的 */var/log/azure-npm.log* 下 | 如需詳細資訊，請參閱 [Calico 元件記錄][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>建立 AKS 叢集並啟用網路原則

若要查看運作中的網路原則，讓我們建立而後展開可定義流量流程的原則：

* 拒絕流向 Pod 的所有流量。
* 允許以 Pod 標籤為基礎的流量。
* 允許以命名空間為基礎的流量。

首先，讓我們建立支援網路原則的 AKS 叢集。 

> [!IMPORTANT]
>
> 只有在建立叢集時，才可以啟用網路原則功能。 您無法在現有的 AKS 叢集上啟用網路原則。

若要使用 Azure 網路原則，您必須使用 [Azure CNI 外掛程式][azure-cni]並定義自己的虛擬網路和子網路。 如需有關如何規劃出必要子網路範圍的詳細資訊，請參閱[設定進階網路][use-advanced-networking]。 Calico 網路原則可以與這個相同的 Azure CNI 外掛程式或與 Kubenet CNI 外掛程式搭配使用。

下列範例指令碼：

* 建立虛擬網路和子網路。
* 建立 Azure Active Directory (Azure AD) 服務主體，以便搭配 AKS 叢集使用。
* 針對虛擬網路上的 AKS 叢集服務主體指派「參與者」權限。
* 在定義的虛擬網路中建立 AKS 叢集，然後啟用網路原則。
    * 即會使用 _Azure 網路_原則選項。 若要改用 Calico 做為網路原則選項，請使用 `--network-policy calico` 參數。 注意:Calico 可以搭配 `--network-plugin azure` 或 `--network-plugin kubenet` 使用。

請注意，不是使用服務主體，而是您可以使用受控識別來取得權限。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。

提供您自己的安全 *SP_PASSWORD*。 您可以取代 *RESOURCE_GROUP_NAME* 和 *CLUSTER_NAME* 變數：

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

建立叢集需要幾分鐘的時間。 當叢集備妥時，請使用 [az aks get-credentials][az-aks-get-credentials] 命令，設定 `kubectl` 以連線到 Kubernetes 叢集。 此命令會下載憑證並設定 Kubernetes CLI 以供使用：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒絕流向 Pod 的所有輸入流量

在您定義規則以允許特定網路流量之前，先建立網路原則以拒絕所有流量。 此原則讓您能夠開始建立僅限所需流量的允許清單。 您可以也清楚地看到套用網路原則時會捨棄該流量。

針對範例應用程式環境和流量規則，讓我們先建立名為 *development* 的命名空間以執行範例 Pod：

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

建立可執行 NGINX 的範例後端 Pod。 這個後端 Pod 可用於模擬範例後端 Web 應用程式。 在 *development* 命名空間中建立這個 Pod，然後開啟連接埠 *80* 來處理 Web 流量。 替 Pod 加上 *app=webapp,role=backend* 標籤，以便我們在下一節中使用網路原則以其作為目標：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

建立另一個 Pod，然後連結終端機工作階段，來測試您是否可以成功觸達預設 NGINX 網頁：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來確認您可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

下列範例輸出會顯示傳回的預設 NGINX 網頁：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>建立並套用網路原則

既然已確認您可以在範例後端 Pod 上使用基本 NGINX 網頁，請建立網路原則以拒絕所有流量。 建立名為 `backend-policy.yaml` 的檔案，並貼上下列 YAML 資訊清單。 此資訊清單會使用 *podSelector* 將原則連結至具有 *app:webapp,role:backend* 標籤的 Pod，例如您的範例 NGINX Pod。 *ingress* 之下未定義任何規則，因此會拒絕流向 Pod 的所有輸入流量：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

移至 [https://shell.azure.com](https://shell.azure.com)，以在瀏覽器中開啟 Azure Cloud Shell。

使用 [kubectl apply][kubectl-apply] 命令來套用網路原則，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>測試網路原則

讓我們看看您是否可以在後端 Pod 上再次使用 NGINX 網頁。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁。 此時，將逾時值設定為 2 秒。 網路原則現在會封鎖所有輸入流量，因此無法載入此頁面，如下列範例所示：

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允許以 Pod 標籤為基礎的輸入流量

在上一節中，已排程後端 NGINX Pod，並建立了網路原則以拒絕所有流量。 讓我們建立前端 Pod 並更新網路原則，以允許來自前端 Pod 的流量。

更新網路原則，以允許來自任何命名空間中具有 *app:webapp,role:frontend* 標籤的 Pod 流量。 編輯先前的 *backend-policy.yaml* 檔案，然後新增 *matchLabels* 輸入規則，讓您的資訊清單看起來如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 此網路原則針對輸入規則使用 *namespaceSelector* 和 *podSelector* 元素。 YAML 語法對於附加輸入規則非常重要。 在此範例中，兩個元素都必須與要套用的輸入規則相符。 在 Kubernetes *1.12* 之前的版本，可能無法如您所預期般正確地轉譯這些元素並限制網路流量。 如需此行為的詳細資訊，請參閱[選取器的來回行為][policy-rules]。

使用 [kubectl apply][kubectl-apply] 命令來套用已更新的網路原則，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f backend-policy.yaml
```

排程標籤為 *app=webapp,role=frontend* 的 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend
```

因為輸入規則允許具有 *app: webapp,role: frontend* 標籤的 Pod 流量，所以允許來自前端 Pod 的流量。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 Pod 會自動刪除。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>測試沒有相符標籤的 Pod

網路原則允許來自標籤為 *app: webapp,role: frontend* 的 Pod 流量，但應該拒絕所有其他流量。 讓我們測試以查看另一個沒有這些標籤的 Pod 是否可以存取後端 NGINX Pod。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看您是否可以存取預設 NGINX 網頁。 網路原則會封鎖輸入流量，因此無法載入此頁面，如下列範例所示：

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>只允許來自已定義命名空間的流量

在上述範例中，您建立了可拒絕所有流量的網路原則，然後更新了此原則，以允許來自具有特定標籤的 Pod 流量。 另一個常見需求是讓流量僅限於指定的命名空間內。 如果先前的範例是針對 *development* 命名空間中的流量，請建立網路原則，以防止來自另一個命名空間 (例如 *production*) 的流量觸達 Pod。

首先，建立新的命名空間，以模擬生產命名空間：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在標籤為 *app=webapp,role=frontend* 的 *production* 命名空間中排程測試 Pod。 連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來確認您可以存取預設 NGINX 網頁：

```console
wget -qO- http://backend.development
```

因為 Pod 的標籤符合網路原則目前許可的內容，所以允許流量。 網路原則不會查看命名空間，只會查看 Pod 標籤。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除。

```console
exit
```

### <a name="update-the-network-policy"></a>更新網路原則

讓我們更新輸入規則 *namespaceSelector* 區段，只允許來自 *development* 命名空間的流量。 編輯 *backend-policy.yaml* 資訊清單檔，如下列範例所示：

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

在更複雜的範例中，您可以定義多個輸入規則，例如依序使用 *namespaceSelector* 和 *podSelector*。

使用 [kubectl apply][kubectl-apply] 命令來套用已更新的網路原則，並指定 YAML 資訊清單的名稱：

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>測試已更新的網路原則

在 *production* 命名空間中排程另一個 Pod，然後連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看網路原則現在是否會拒絕流量：

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

結束測試 Pod：

```console
exit
```

拒絕了來自 *production* 命名空間的流量，回到 *development* 命名空間中排程測試 Pod，然後連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在殼層提示字元，使用 `wget` 來查看網路原則是否允許流量：

```console
wget -qO- http://backend
```

因為 Pod 已排定在符合網路原則許可內容的命名空間中，所以允許流量。 下列範例輸出會顯示傳回的預設 NGINX 網頁：

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試 Pod 會自動刪除。

```console
exit
```

## <a name="clean-up-resources"></a>清除資源

在本文中，我們已建立兩個命名空間並套用網路原則。 若要清除這些資源，請使用 [kubectl delete][kubectl-delete] 命令並指定資源名稱：

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>後續步驟

如需網路資源的詳細資訊，請參閱 [Azure Kubernetes Service (AKS) 中應用程式的網路概念][concepts-network]。

若要深入了解原則，請參閱 [Kubernetes 網路原則][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
