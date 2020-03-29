---
title: 將靜態 IP 位址和 DNS 標籤與 Azure 庫伯奈斯服務 （AKS） 負載等化器一起使用
description: 了解如何搭配 Azure Kubernetes Service (AKS) 負載平衡器來建立和使用靜態 IP 位址。
services: container-service
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: 6c219976db21fb05ea1ad313b4effdf95906f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047960"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>將靜態公共 IP 位址和 DNS 標籤與 Azure 庫伯奈斯服務 （AKS） 負載等化器一起使用

根據預設，指派給 AKS 叢集所建立之負載平衡器資源的公用 IP 位址，只有在該資源的生命週期內有效。 如果您刪除 Kubernetes 服務，相關聯的負載平衡器和 IP 位址也會一併刪除。 如果您想要針對已重新部署的 Kubernetes 服務指派特定的 IP 位址或保留 IP 位址，您可以建立並使用靜態公用 IP 位址。

此文章示範如何建立靜態公用 IP 位址，並將它指派給您的 Kubernetes 服務。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您還需要 Azure CLI 版本 2.0.59 或更高版本安裝和配置。 執行  `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱 [安裝 Azure CLI][install-azure-cli]。

本文介紹使用*標準*SKU IP 和*標準*SKU 負載等化器。 有關詳細資訊，請參閱[Azure 中的 IP 位址類型和分配方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>建立靜態 IP 位址

使用[az 網路公共 IP 創建命令創建][az-network-public-ip-create]靜態公共 IP 位址。 以下在*myResourceGroup 資源*組中創建名為*myAKSPublicIP*的靜態 IP 資源：

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> 如果在 AKS 群集中使用*基本*SKU 負載等化器，則在定義公共 IP 時對*sKU*參數使用*Basic。* 只有*基本*SKU IP 與*基本*SKU 負載等化器配合使用，只有*標準*SKU IP 與*標準*SKU 負載等化器一起工作。 

將顯示 IP 位址，如以下壓縮示例輸出所示：

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

您稍後可以使用 [az network public-ip list][az-network-public-ip-list] 命令來取得公用 IP 位址。 指定節點資源群組的名稱以及您建立的公用 IP 位址，然後查詢 ipAddress**，如下列範例所示：

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用靜態 IP 位址建立服務

在創建服務之前，請確保 AKS 群集使用的服務主體將許可權委派給其他資源組。 例如：

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

或者，您可以使用為許可權分配的系統分配託管標識，而不是服務主體。 有關詳細資訊，請參閱[使用託管標識](use-managed-identity.md)。

要使用靜態公共 IP 位址創建*LoadBalancer*服務，`loadBalancerIP`請將靜態公共 IP 位址的屬性和值添加到 YAML 清單中。 建立名為 `load-balancer-service.yaml` 的檔案，然後將下列 YAML 複製進來。 提供您在上一個步驟中所建立的自有公用 IP 位址。 下面的示例還將注釋設置到名為*myResourceGroup*的資源組。 提供您自己的資源組名稱。

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

使用 `kubectl apply` 命令建立服務和部署。

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>將 DNS 標籤應用於服務

如果您的服務使用動態或靜態公共 IP 位址，則可以使用服務注釋`service.beta.kubernetes.io/azure-dns-label-name`設置面向公眾的 DNS 標籤。 這將使用 Azure 的公共 DNS 伺服器和頂層網域為服務發佈完全限定的功能變數名稱。 注釋值在 Azure 位置中必須是唯一的，因此建議使用足夠限定的標籤。   

然後，Azure 將自動將預設子網（`<location>.cloudapp.azure.com`位置是您選擇的區域）追加到您提供的名稱中，以創建完全限定的 DNS 名稱。 例如：

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> 要在自己的域上發佈服務，請參閱[Azure DNS][azure-dns-zone]和[外部 dns][external-dns]專案。

## <a name="troubleshoot"></a>疑難排解

如果 Kubernetes 服務清單的*loadBalancerIP*屬性中定義的靜態 IP 位址不存在，或者未在節點資源組中創建，並且未配置其他任務，則負載等化器服務創建將失敗。 若要進行疑難排解，請使用 [kubectl describe][kubectl-describe] 命令來檢閱服務建立事件。 提供 YAML 資訊清單中所指定的服務名稱，如下列範例所示：

```console
kubectl describe service azure-load-balancer
```

Kubernetes 服務資源的相關資訊即會顯示。 下列範例輸出結尾的 *Events* 指出「找不到使用者提供的 IP 位址」**。 在這些案例中，請確認您已在節點資源群組中建立靜態公用 IP 位址，以及 Kubernetes 服務資訊清單中所指定的 IP 位址正確無誤。

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>後續步驟

若要進一步控制應用程式的網路流量，您可能想要改為[建立輸入控制器][aks-ingress-basic]。 您也可以[使用靜態公用 IP 位址建立輸入控制器][aks-static-ingress]。

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
