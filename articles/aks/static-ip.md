---
title: 搭配使用靜態 IP 與負載平衡器
titleSuffix: Azure Kubernetes Service
description: 了解如何搭配 Azure Kubernetes Service (AKS) 負載平衡器來建立和使用靜態 IP 位址。
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 22fd099633556fa9ddce575c2ac238b4950667cb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651884"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>使用靜態公用 IP 位址和 DNS 標籤搭配 Azure Kubernetes Service (AKS) 負載平衡器

根據預設，指派給 AKS 叢集所建立之負載平衡器資源的公用 IP 位址，只有在該資源的生命週期內有效。 如果您刪除 Kubernetes 服務，相關聯的負載平衡器和 IP 位址也會一併刪除。 如果您想要針對已重新部署的 Kubernetes 服務指派特定的 IP 位址或保留 IP 位址，您可以建立並使用靜態公用 IP 位址。

此文章示範如何建立靜態公用 IP 位址，並將它指派給您的 Kubernetes 服務。

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

您也必須安裝並設定 Azure CLI 2.0.59 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI][install-azure-cli]。

本文涵蓋使用 *標準* sku IP 與 *標準* sku 負載平衡器。 如需詳細資訊，請參閱 [Azure 中的 IP 位址類型及配置方法][ip-sku]。

## <a name="create-a-static-ip-address"></a>建立靜態 IP 位址

使用 [az network public ip create][az-network-public-ip-create] 命令來建立靜態公用 ip 位址。 以下會在 *myResourceGroup* 資源群組中建立名為 *MYAKSPUBLICIP* 的靜態 IP 資源：

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> 如果您在 AKS 叢集中使用 *基本* SKU 負載平衡器，則在定義公用 IP 時，請使用 *基本* 的 *sku* 參數。 只有 *基本* sku ip 適用于 *基本* sku 負載平衡器，而且只有 *標準* sku ip 適用于 *標準* sku 負載平衡器。 

會顯示 IP 位址，如下列壓縮範例輸出所示：

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

您稍後可以使用 [az network public-ip list][az-network-public-ip-list] 命令來取得公用 IP 位址。 指定節點資源群組的名稱以及您建立的公用 IP 位址，然後查詢 ipAddress，如下列範例所示：

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>使用靜態 IP 位址建立服務

建立服務之前，請確定 AKS 叢集所使用的服務主體已將許可權委派給其他資源群組。 例如：

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

或者，您可以將系統指派的受控識別用於許可權，而非服務主體。 如需詳細資訊，請參閱[使用受控識別](use-managed-identity.md)。

> [!IMPORTANT]
> 如果您自訂了輸出 IP，請確定您的叢集身分識別具有輸出公用 IP 和此輸入公用 IP 的許可權。

若要建立具有靜態公用 IP 位址的 *LoadBalancer* 服務，請將 `loadBalancerIP` 靜態公用 ip 位址的屬性和值新增至 YAML 資訊清單。 建立名為 `load-balancer-service.yaml` 的檔案，然後將下列 YAML 複製進來。 提供您在上一個步驟中所建立的自有公用 IP 位址。 下列範例也會將批註設定為名為 *myResourceGroup* 的資源群組。 提供您自己的資源組名。

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

## <a name="apply-a-dns-label-to-the-service"></a>將 DNS 標籤套用至服務

如果您的服務使用動態或靜態公用 IP 位址，您可以使用服務注釋 `service.beta.kubernetes.io/azure-dns-label-name` 來設定公開的 DNS 標籤。 這會使用 Azure 的公用 DNS 伺服器和最上層網域來發佈服務的完整功能變數名稱。 注釋值在 Azure 位置中必須是唯一的，因此建議使用足夠的完整標籤。   

接著，Azure 會自動將預設子網（例如 `<location>.cloudapp.azure.com` (位置是您) 所選的區域）附加到您所提供的名稱，以建立完整的 DNS 名稱。 例如：

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
> 若要在您自己的網域上發佈服務，請參閱 [Azure DNS][azure-dns-zone] 和 [外部 DNS][external-dns] 專案。

## <a name="troubleshoot"></a>疑難排解

如果 Kubernetes 服務資訊清單的 *loadBalancerIP* 屬性中定義的靜態 IP 位址不存在，或尚未在節點資源群組中建立，且未設定其他委派，則負載平衡器服務建立會失敗。 若要進行疑難排解，請使用 [kubectl describe][kubectl-describe] 命令來檢閱服務建立事件。 提供 YAML 資訊清單中所指定的服務名稱，如下列範例所示：

```console
kubectl describe service azure-load-balancer
```

Kubernetes 服務資源的相關資訊即會顯示。 下列範例輸出結尾的 *Events* 指出「找不到使用者提供的 IP 位址」。 在這些案例中，請確認您已在節點資源群組中建立靜態公用 IP 位址，以及 Kubernetes 服務資訊清單中所指定的 IP 位址正確無誤。

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
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
