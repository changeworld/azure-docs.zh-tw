---
title: 使用網路政策保護 Pod 流量
titleSuffix: Azure Kubernetes Service
description: 瞭解如何使用 Azure Kubernetes 服務 (AKS) 中的庫伯內斯網路策略來保護進出窗格的流量
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 01ba9e7353b6783d1b4fd1649291a64405fd9382
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886682"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 中使用網路原則來保護 Pod 之間的流量

當您在 Kubernetes 中執行新式微服務架構的應用程式時，您通常想要控制哪些元件可以彼此通訊。 最小特權原則應用於流量如何在 Azure Kubernetes 服務 (AKS) 群集中的窗格之間流動。 假設您可能希望直接阻止流量到後端應用程式。 Kubernetes 中的 *「網路策略*」功能允許您為群集中 pod 之間的入口和出口流量定義規則。

本文介紹如何安裝網路策略引擎並創建 Kubernetes 網路策略來控制 AKS 中 pod 之間的流量。 網路策略應僅用於 AKS 中基於 Linux 的節點和 pod。

## <a name="before-you-begin"></a>開始之前

您需要 Azure CLI 版本 2.0.61 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

> [!TIP]
> 如果在預覽期間使用了網路策略功能,我們建議您[建立新的叢集](#create-an-aks-cluster-and-enable-network-policy)。
> 
> 如果希望繼續使用在預覽期間使用網路策略的現有測試群集,請將群集升級到最新的 GA 版本的新 Kubernetes 版本,然後部署以下 YAML 清單來修復崩潰的指標伺服器和 Kubernetes 儀表板。 此修復僅適用於使用 Calico 網路策略引擎的群集。
>
> 作為安全最佳實務,[請查看此 YAML 清單的內容][calico-aks-cleanup],以瞭解部署在 AKS 群集中的內容。
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>網路原則概觀

默認情況下,AKS 群集中的所有 Pod 都可以不受限制地發送和接收流量。 為了提升安全性，您可以定義可控制流量流程的規則。 例如,後端應用程式通常僅暴露於所需的前端服務。 或者,資料庫元件只能由連接到它們的應用程式層訪問。

網路策略是一個 Kubernets 規範,用於定義 Pod 之間通訊的訪問策略。 使用網路策略,定義一組有序的規則來發送和接收流量,並將它們應用於與一個或多個標籤選擇器匹配的窗格集合。

這些網路策略規則定義為 YAML 清單。 網路策略可以包含在創建部署或服務的更廣泛清單中。

### <a name="network-policy-options-in-aks"></a>AKS 中的網路原則選項

Azure 提供了兩種實現網路策略的方法。 創建 AKS 群集時,請選擇網路策略選項。 建立叢集後無法變更策略選項:

* Azure 自己的實現,稱為*Azure 網路原則*。
* *卡利科網路政策*,一個開源網路和網路安全解決方案由[Tigera][tigera]建立。

這兩個實現都使用 Linux *IPTables*來強制實施指定的策略。 策略被轉換為允許和不允許的 IP 對集。 然後,這些對被程式設計為 IPTable 篩選器規則。

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 和 Calico 政策及其功能之間的差異

| 功能                               | Azure                      | 印花布                      |
|------------------------------------------|----------------------------|-----------------------------|
| 支援的平台                      | Linux                      | Linux                       |
| 支援的網路選項             | Azure CNI                  | Azure CNI 和 kubenet       |
| 符合庫伯內斯規範 | 支援所有政策類型 |  支援所有政策類型 |
| 其他功能                      | None                       | 擴展策略模型,由全域網路策略、全域網路集和主機終結點組成。 有關使用`calicoctl`CLI 管理這些擴充功能的詳細資訊,請參閱[calicoctl 使用者參考][calicoctl]。 |
| 支援                                  | Azure 支援與專案團隊支援 | 卡利科社區支援。 有關其他付費支援的詳細資訊,請參閱[卡利科專案支援選項][calico-support]。 |
| 記錄                                  | 在 IPTables 新增/刪除規則在 */var/log/azure-npm.log*下記錄在每台主機上 | 有關詳細資訊,請參閱[卡利科組件日誌][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>建立 AKS 叢集並啟用網路原則

要檢視網路原則的操作,讓我們建立並展開定義流量串流的策略:

* 拒絕流向 Pod 的所有流量。
* 允許以 Pod 標籤為基礎的流量。
* 允許以命名空間為基礎的流量。

首先,讓我們創建一個支援網路策略的 AKS 群集。 

> [!IMPORTANT]
>
> 僅當創建群集時,才能啟用網路策略功能。 您無法在現有的 AKS 叢集上啟用網路原則。

要使用 Azure 網路策略,必須使用[Azure CNI 外掛程式][azure-cni]並定義自己的虛擬網路和子網。 如需有關如何規劃出必要子網路範圍的詳細資訊，請參閱[設定進階網路][use-advanced-networking]。 Calico 網路策略可以與同一 Azure CNI 外掛程式一起使用,也可以與 Kubenet CNI 外掛程式一起使用。

下列範例指令碼：

* 建立虛擬網路和子網路。
* 創建 Azure 活動目錄 (Azure AD) 服務主體,以便與 AKS 群集一起使用。
* 針對虛擬網路上的 AKS 叢集服務主體指派「參與者」** 權限。
* 在定義的虛擬網路中創建 AKS 群集,並啟用網路策略。
    * 使用*azure*網路策略選項。 要使用 Calico 作為網路策略選項,`--network-policy calico`請使用 參數。 注意:Calico 可以與`--network-plugin azure``--network-plugin kubenet`或 一起使用。

提供您自己的安全 *SP_PASSWORD*。 您可以取代*RESOURCE_GROUP_NAME*變數和*CLUSTER_NAME*變數:

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

建立叢集需要幾分鐘的時間。 群集準備就緒后,使用 az `kubectl` [aks 獲取憑據][az-aks-get-credentials]命令配置為連接到 Kubernetes 群集。 此命令會下載憑證並設定 Kubernetes CLI 以供使用：

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>拒絕流向 Pod 的所有輸入流量

在您定義規則以允許特定網路流量之前，先建立網路原則以拒絕所有流量。 此策略為您提供了開始僅將所需流量列入白名單的起點。 您可以也清楚地看到套用網路原則時會捨棄該流量。

對於範例應用程式環境和流量規則,讓我們首先建立一個稱為*開發*的名稱空間來執行範例窗格:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

建立執行 NGINX 的範例後端窗格。 此後端窗格可用於類比基於 Web 的範例應用程式。 在 *development* 命名空間中建立這個 Pod，然後開啟連接埠 *80* 來處理 Web 流量。 替 Pod 加上 *app=webapp,role=backend* 標籤，以便我們在下一節中使用網路原則以其作為目標：

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

建立另一個 Pod 並附加終端機工作階段以測試您是否能夠成功存取預設 NGINX 網頁:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell`wget`提示符 下,用於確認可以存取預設 NGINX 網頁:

```console
wget -qO- http://backend
```

以下範例輸出顯示預設 NGINX 網頁傳回:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試窗格將自動刪除。

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>建立並套用網路原則

現在,您已經確認可以在示例後埠上使用基本的 NGINX 網頁,請創建網路策略來拒絕所有流量。 建立名為 `backend-policy.yaml` 的檔案，並貼上下列 YAML 資訊清單。 此清單使用*podSelector*將策略附加到具有*應用:webapp、角色:後端*標籤的窗格,如示例 NGINX 窗格。 *ingress* 之下未定義任何規則，因此會拒絕流向 Pod 的所有輸入流量：

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

轉到[https://shell.azure.com](https://shell.azure.com)在瀏覽器中打開 Azure 雲外殼。

使用[kubectl 應用程式應用程式,][kubectl-apply]並指定 YAML 清單的名稱:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>測試網路原則

讓我們看看是否可以再次使用後端窗格上的 NGINX 網頁。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell`wget`提示 符 下,使用 以檢視是否可以造訪預設的 NGINX 網頁。 此時，將逾時值設定為 2** 秒。 網路策略現在阻止所有入站流量,因此無法載入頁面,如以下範例所示:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

結束連結的終端機工作階段。 測試窗格將自動刪除。

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>允許以 Pod 標籤為基礎的輸入流量

在上一節中,計劃了後端NGINXpod,並創建了一個網路策略來拒絕所有流量。 讓我們創建一個前端窗格並更新網路策略,以允許來自前端 pod 的流量。

更新網路原則，以允許來自任何命名空間中具有 *app:webapp,role:frontend* 標籤的 Pod 流量。 編輯以前的*後端介面原則.yaml*檔案,並加入*符合分頁*入口規則,以便您的清單看起來像以下範例:

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
> 此網路原則針對輸入規則使用 *namespaceSelector* 和 *podSelector* 元素。 YAML 語法對於入口規則是累加的非常重要。 在此範例中，兩個元素都必須與要套用的輸入規則相符。 *1.12*之前的 Kubernetes 版本可能無法正確解釋這些元素,並按預期限制網路流量。 有關此行為的更多,請參閱[選擇器 的/和。][policy-rules]

使用[kubectl 應用程式使用應用程式][kubectl-apply]更新的網路原則,並指定 YAML 清單的名稱:

```console
kubectl apply -f backend-policy.yaml
```

計劃標記為*app_webapp、角色_前端*並附加終端會話的窗格:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell`wget`提示符 下,使用 以檢視是否可以造訪預設 NGINX 網頁:

```console
wget -qO- http://backend
```

因為入口規則允許具有標籤應用的窗格的流量 *:Webapp,角色:前端*,允許來自前端窗格的流量。 以下範例輸出顯示傳回的預設 NGINX 網頁:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 該窗格將自動刪除。

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>測試沒有相符標籤的 Pod

網路原則允許來自標籤為 *app: webapp,role: frontend* 的 Pod 流量，但應該拒絕所有其他流量。 讓我們測試一下,如果沒有這些標籤的另一個窗格是否可以訪問後端 NGINX 窗格。 建立另一個測試 Pod 並連結終端機工作階段：

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

在 shell`wget`提示 符 下,使用 以檢視是否可以造訪預設的 NGINX 網頁。 網路策略阻止入站流量,因此無法載入頁面,如以下範例所示:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

結束連結的終端機工作階段。 測試窗格將自動刪除。

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>只允許來自已定義命名空間的流量

在前面的範例中,您創建了一個網路策略,該策略拒絕所有流量,然後更新策略以允許來自具有特定標籤的 Pod 的流量。 另一個常見的需要是限制流量僅在給定命名空間內。 如果前面的範例用於*開發*命名空間中的流量,請建立一個網路策略,以防止來自另一個命名空間(如*生產*)的流量到達 pod。

首先，建立新的命名空間，以模擬生產命名空間：

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

在標籤為 *app=webapp,role=frontend* 的 *production* 命名空間中排程測試 Pod。 連結終端機工作階段：

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell`wget`提示符 下,用於確認可以存取預設 NGINX 網頁:

```console
wget -qO- http://backend.development
```

由於 pod 的標籤與網路策略中當前允許的標籤匹配,因此允許流量。 網路原則不會查看命名空間，只會查看 Pod 標籤。 以下範例輸出顯示傳回的預設 NGINX 網頁:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試窗格將自動刪除。

```console
exit
```

### <a name="update-the-network-policy"></a>更新網路原則

讓我們更新入口規則*命名空間選擇器*部分,以僅允許來自*開發*命名空間中的流量。 編輯 *backend-policy.yaml* 資訊清單檔，如下列範例所示：

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

在更複雜的示例中,可以定義多個入口規則,如*命名空間選擇器*,然後是*podSelector。*

使用[kubectl 應用程式使用應用程式][kubectl-apply]更新的網路原則,並指定 YAML 清單的名稱:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>測試已更新的網路原則

在*生產*命名空間中安排另一個窗格並附加終端機階段:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

在 shell`wget`提示符 下,使用 以檢視網路策略現在拒絕流量:

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

當從*生產*命名空間拒絕流量時,將測試窗格安排回*開發*命名空間並附加終端工作階段:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

在 shell`wget`提示符 下,使用 以檢視網路原則允許流量:

```console
wget -qO- http://backend
```

允許流量,因為 pod 安排在與網路策略中允許的內容相匹配的命名空間中。 以下範例輸出顯示傳回的預設 NGINX 網頁:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

結束連結的終端機工作階段。 測試窗格將自動刪除。

```console
exit
```

## <a name="clean-up-resources"></a>清除資源

在本文中,我們創建了兩個命名空間並應用了網路策略。 要清理這些資源,請使用[kubectl 刪除][kubectl-delete]指令並指定資源名稱:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>後續步驟

有關網路資源的更多資訊,請參閱[Azure 庫伯奈斯服務 (AKS) 中應用程式的網路概念][concepts-network]。

要瞭解有關策略的更多資訊,請參閱[庫伯內斯網路策略][kubernetes-network-policies]。

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
