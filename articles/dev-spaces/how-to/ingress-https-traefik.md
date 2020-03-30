---
title: 使用自訂特徵入侵控制器並配置 HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: 瞭解如何配置 Azure 開發人員空間以使用自訂特徵入侵控制器，並使用該入口控制器配置 HTTPS
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155424"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>使用自訂特徵入侵控制器並配置 HTTPS

本文介紹如何配置 Azure 開發空間以使用自訂特徵入侵控制器。 本文還介紹了如何配置該自訂入口控制器以使用 HTTPS。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有帳戶，您可以建立[免費帳戶][azure-account-create]。
* [已安裝 Azure CLI][az-cli]。
* [啟用 Azure 開發空間的 Azure 庫伯奈斯服務 （AKS） 群集][qs-cli]。
* [已安裝庫布克特爾][kubectl]。
* [已安裝 Helm 3][helm-installed]。
* 具有[DNS 區域][dns-zone]的[自訂域][custom-domain]。 本文假定自訂域和 DNS 區域與 AKS 群集位於同一資源組中，但可以在其他資源組中使用自訂域和 DNS 區域。

## <a name="configure-a-custom-traefik-ingress-controller"></a>配置自訂特徵入侵控制器

使用庫貝內特斯命令列用戶端[庫布埃特爾][kubectl]連接到群集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

添加[官方穩定的Helm存儲庫][helm-stable-repo]，其中包含入侵控制器Helm圖表。

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

為 Traefik 入口控制器創建 Kubernetes 命名空間，並使用`helm`安裝它。

> [!NOTE]
> 如果您的 AKS 群集未啟用 RBAC，請刪除 *--set rbac.啟用_true*參數。

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> 上面的示例為入口控制器創建公共終結點。 如果需要對入口控制器使用專用終結點，請添加 *--set 服務.注釋。服務\\\\.Beta\\.kubernetes .io/azure 負載等化器內部"=掌**舵安裝*命令的真實參數。
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> 此專用終結點在部署 AKS 群集的虛擬網路中公開。

使用[kubectl get][kubectl-get]獲取 traefik 入口控制器服務的 IP 位址。

```console
kubectl get svc -n traefik --watch
```

示例輸出顯示*traefik*名稱空間中所有服務的 IP 位址。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

使用[az 網路 dns 記錄設置添加記錄][az-network-dns-record-set-a-add-record]，將*A*記錄添加到具有 traefik 服務外部 IP 位址的 DNS 區域。

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

上面的示例將*A*記錄添加到*MY_CUSTOM_DOMAIN* DNS 區域。

在本文中，您會使用 [Azure Dev Spaces 單車共享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) \(英文\) 來示範如何使用 Azure Dev Spaces。 從 GitHub 複製應用程式，並瀏覽至其目錄：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

打開[值.yaml][values-yaml]並進行以下更新：
* 將 *<REPLACE_ME_WITH_HOST_SUFFIX>* 的所有實例替換為*traefik。MY_CUSTOM_DOMAIN*將域用於*MY_CUSTOM_DOMAIN*。 
* 替換*kubernetes.io/ingress.class： traefik-azds = 專用的開發人員空間**kubernetes.io/ingress.class： traefik = 自訂入口*。 

下面是更新`values.yaml`檔的示例：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

儲存變更並關閉該檔案。

使用`azds space select`使用 使用 應用程式範例創建*開發*空間。

```console
azds space select -n dev -y
```

使用`helm install`部署應用程式範例。

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

上面的示例將應用程式範例部署到*開發*命名空間。

顯示使用 訪問應用程式範例的`azds list-uris`URL。

```console
azds list-uris
```

下面的輸出顯示了 來自`azds list-uris`的示例 URL。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

開啟來自 `azds list-uris` 命令的公用 URL，來瀏覽至 *bikesharingweb* 服務。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`。

> [!NOTE]
> 如果您看到錯誤頁而不是*自行車共用 Web*服務，請驗證更新值**both***.yaml*檔中*的kubernetes.io/ingress.class*注釋和主機。

使用`azds space select`命令在*開發*下創建子空間，並列出用於訪問子開發空間的 URL。

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

下面的輸出顯示了用於`azds list-uris`訪問*azureuser1*子開發空間中的應用程式範例的示例 URL。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

通過從`azds list-uris`命令打開公共 URL，導航到*azureuser1*子開發空間中的*自行車共用 Web*服務。 在上面的示例中 *，azureuser1*子開發空間中的*自行車共用 Web*服務的公共 URL 是`http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`。

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>將特徵入口控制器配置為使用 HTTPS

在配置 traefik 入口控制器以使用 HTTPS 時，使用[證書管理器][cert-manager]自動管理 TLS 證書。 用於`helm`安裝*證書管理器*圖表。

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

創建`letsencrypt-clusterissuer.yaml`檔，並更新電子郵件欄位與您的電子郵件地址。

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
      - http01:
          ingress:
            class: traefik
```

> [!NOTE]
> 對於測試，還有一個[暫存伺服器][letsencrypt-staging-issuer]可用於*群集頒發者*。

用於`kubectl`應用`letsencrypt-clusterissuer.yaml`。

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

刪除以前的*Traefik* *群集角色*和*群集角色綁定*，然後升級 traefik 以使用`helm`HTTPS。

> [!NOTE]
> 如果您的 AKS 群集未啟用 RBAC，請刪除 *--set rbac.啟用_true*參數。

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

使用[kubectl get][kubectl-get]獲取特拉菲克入口控制器服務的更新 IP 位址。

```console
kubectl get svc -n traefik --watch
```

示例輸出顯示*traefik*名稱空間中所有服務的 IP 位址。

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

使用[az 網路 dns 記錄設置 add-sa][az-network-dns-record-set-a-add-record]服務的新外部 IP 位址將*A*記錄添加到您的 DNS 區域，*A*並使用[az 網路 dns 記錄設置刪除記錄][az-network-dns-record-set-a-remove-record]。

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

上面的示例更新*MY_CUSTOM_DOMAIN* DNS 區域中的*A*記錄以使用*PREVIOUS_EXTERNAL_IP*。

更新[值.yaml][values-yaml]以包括用於使用*證書管理器*和 HTTPS 的詳細資訊。 下面是更新`values.yaml`檔的示例：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

使用`helm`升級應用程式範例：

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

導航到*dev/azureuser1*子空間中的應用程式範例，並注意到您將被重定向到使用 HTTPS。

> [!IMPORTANT]
> 完成 DNS 更改可能需要 30 分鐘或更長時間，並且可以訪問應用程式範例。

另請注意頁面載入，但瀏覽器顯示一些錯誤。 打開瀏覽器主控台將顯示錯誤與嘗試載入 HTTP 資源的 HTTPS 頁相關。 例如：

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

要修復此錯誤，請更新[Bike 共用Web/azds.yaml][azds-yaml]以使用*traefik* kubernetes.io/ingress.class和自訂域為 *$（主機Suffix）*。 *kubernetes.io/ingress.class* 例如：

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

使用*URL*包的依賴項更新[自行車共用Web/包.json。][package-json]

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

在[自行車共用Web/lib/helpers.js][helpers-js]中更新*getApiHostAsync*方法以使用 HTTPS：

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

導航到`BikeSharingWeb`目錄並用於`azds up`運行更新的自行車共用 Web 服務。

```console
cd ../BikeSharingWeb/
azds up
```

導航到*dev/azureuser1*子空間中的應用程式範例，並注意到您將被重定向到使用 HTTPS，沒有任何錯誤。

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml