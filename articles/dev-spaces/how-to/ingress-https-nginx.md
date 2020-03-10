---
title: 使用自訂 NGINX 輸入控制器並設定 HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: 瞭解如何設定 Azure Dev Spaces 以使用自訂 NGINX 輸入控制器，並使用該輸入控制器來設定 HTTPS
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 容器, Helm, 服務網格, 服務網格路由傳送, kubectl, k8s
ms.openlocfilehash: 2debe6daf409200059f28aef52202223193e8f0c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389684"
---
# <a name="use-a-custom-nginx-ingress-controller-and-configure-https"></a>使用自訂 NGINX 輸入控制器並設定 HTTPS

本文說明如何將 Azure Dev Spaces 設定為使用自訂 NGINX 輸入控制器。 本文也會說明如何將該自訂輸入控制器設定為使用 HTTPS。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有帳戶，您可以建立[免費帳戶][azure-account-create]。
* [已安裝 Azure CLI][az-cli]。
* [已啟用 Azure Dev Spaces 的 Azure Kubernetes Service （AKS）][qs-cli]叢集。
* 已安裝[kubectl][kubectl] 。
* [已安裝 Helm 3][helm-installed]。
* 具有[DNS 區域][dns-zone][的自訂網域][custom-domain]。  本文假設自訂網域和 DNS 區域位於與您的 AKS 叢集相同的資源群組中，但您可以在不同的資源群組中使用自訂網域和 DNS 區域。

## <a name="configure-a-custom-nginx-ingress-controller"></a>設定自訂 NGINX 輸入控制器

使用[kubectl][kubectl]（Kubernetes 命令列用戶端）連接到您的叢集。 若要設定 `kubectl` 以連線到 Kubernetes 叢集，請使用 [az aks get-credentials][az-aks-get-credentials] 命令。 此命令會下載憑證並設定 Kubernetes CLI 以供使用。

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

若要驗證針對您叢集的連線，請使用 [kubectl get][kubectl-get] 命令來傳回叢集節點的清單。

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

新增[正式的穩定 Helm 存放庫][helm-stable-repo]，其中包含 NGINX 輸入控制器 Helm 圖表。

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

為 NGINX 輸入控制器建立 Kubernetes 命名空間，並使用 `helm`進行安裝。

```console
kubectl create ns nginx
helm install nginx stable/nginx-ingress --namespace nginx --version 1.27.0
```

> [!NOTE]
> 上述範例會建立輸入控制器的公用端點。 如果您需要改用輸入控制器的私用端點，請新增 *--設定控制器。service\\Beta\\. kubernetes\\。 io/azure-load-平衡器-internal "= true*參數至*helm install*命令。 例如：
> ```console
> helm install nginx stable/nginx-ingress --namespace nginx --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.27.0
> ```
> 此私人端點會在您 AKS 叢集部署所在的虛擬網路中公開。

使用[kubectl get][kubectl-get]取得 NGINX 輸入控制器服務的 IP 位址。

```console
kubectl get svc -n nginx --watch
```

範例輸出會顯示*nginx*命名空間中所有服務的 IP 位址。

```console
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     <pending>        80:31314/TCP,443:30521/TCP   10s
nginx-nginx-ingress-default-backend   ClusterIP      10.0.210.231   <none>           80/TCP                       10s
...
nginx-nginx-ingress-controller        LoadBalancer   10.0.19.39     MY_EXTERNAL_IP   80:31314/TCP,443:30521/TCP   26s
```

使用 az network DNS record，將*a*記錄新增至您的 DNS 區域，並在其中加入 NGINX 服務的外部 IP 位址[-設定新增記錄][az-network-dns-record-set-a-add-record]。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.nginx \
    --ipv4-address MY_EXTERNAL_IP
```

上述範例會將*A*記錄新增至*MY_CUSTOM_DOMAIN* DNS 區域。

在本文中，您會使用 [Azure Dev Spaces 單車共享範例應用程式](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) \(英文\) 來示範如何使用 Azure Dev Spaces。 從 GitHub 複製應用程式，並瀏覽至其目錄：

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

開啟 [[值]。 yaml][values-yaml]並進行下列更新：
* 將 *< REPLACE_ME_WITH_HOST_SUFFIX >* 的所有實例取代為*nginx。MY_CUSTOM_DOMAIN*使用您的網域進行*MY_CUSTOM_DOMAIN*。 
* 以*kubernetes.io/ingress.class： nginx # 自訂*輸入取代*kubernetes.io/ingress.class： traefik-Azds # Dev Spaces 特有*。 

以下是已更新 `values.yaml` 檔案的範例：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

儲存變更並關閉該檔案。

使用 `azds space select`，透過您的範例應用程式建立*開發人員*空間。

```console
azds space select -n dev -y
```

使用 `helm install`部署範例應用程式。

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

上述範例會將範例應用程式部署至*dev*命名空間。

顯示 Url，以使用 `azds list-uris`來存取範例應用程式。

```console
azds list-uris
```

下列輸出顯示來自 `azds list-uris`的範例 Url。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

開啟來自 *命令的公用 URL，來瀏覽至*bikesharingweb`azds list-uris` 服務。 在上述範例中，*bikesharingweb* 服務的公用 URL 是 `http://dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`。

> [!NOTE]
> 如果您看到錯誤頁面，而不是*bikesharingweb*服務，請確認您已在*yaml*檔案**中更新** *kubernetes.io/ingress.class*注釋和主機。

使用 `azds space select` 命令在*dev*底下建立子空間，並列出 url 以存取子開發人員空間。

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

下列輸出顯示 `azds list-uris` 的範例 Url，以存取*azureuser1*子開發人員空間中的範例應用程式。

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/         Available
```

藉由從 `azds list-uris` 命令開啟公用 URL，流覽至*azureuser1*子開發人員空間中的*bikesharingweb*服務。 在上述範例中，會 `http://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/`*azureuser1*子開發人員空間中的*BIKESHARINGWEB*服務公用 URL。

## <a name="configure-the-nginx-ingress-controller-to-use-https"></a>將 NGINX 輸入控制器設定為使用 HTTPS

將 NGINX 輸入控制器設定為使用 HTTPS 時，請使用[cert 管理員][cert-manager]來自動化 TLS 憑證的管理。 使用 `helm` 安裝*certmanager*圖表。

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace nginx
kubectl label namespace nginx certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace nginx --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

建立 `letsencrypt-clusterissuer.yaml` 檔案，並以您的電子郵件地址更新 [電子郵件] 欄位。

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
            class: nginx
```

> [!NOTE]
> 若要進行測試，您也可以使用*ClusterIssuer*的[預備伺服器][letsencrypt-staging-issuer]。

使用 `kubectl` 來套用 `letsencrypt-clusterissuer.yaml`。

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace nginx
```

更新[值。 yaml][values-yaml]以包含使用*cert-管理員*和 HTTPS 的詳細資料。 以下是已更新 `values.yaml` 檔案的範例：

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: nginx  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.nginx.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

使用 `helm`升級範例應用程式：

```console
helm upgrade bikesharing . --namespace dev --atomic
```

流覽至*dev/azureuser1*子空間中的範例應用程式，並注意您會重新導向至使用 HTTPS。 另請注意，頁面會載入，但瀏覽器會顯示一些錯誤。 開啟瀏覽器主控台會顯示與嘗試載入 HTTP 資源的 HTTPS 頁面相關的錯誤。 例如：

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.nginx.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.nginx.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

若要修正此錯誤，請更新[BikeSharingWeb/azds][azds-yaml] ，如下所示：

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: nginx
        cert-manager.io/cluster-issuer: letsencrypt
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
      tls:
      - hosts:
        - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.nginx.MY_CUSTOM_DOMAIN
        secretName: dev-bikesharingweb-secret
...
```

以*url*套件的相依性更新[BikeSharingWeb/package. json][package-json] 。

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

更新[BikeSharingWeb/pages/helper .js][helpers-js]中的*getApiHostAsync*方法，以使用 HTTPS：

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

流覽至 `BikeSharingWeb` 目錄，然後使用 `azds up` 來執行更新的*BikeSharingWeb*服務。

```console
cd ../BikeSharingWeb/
azds up
```

流覽至*dev/azureuser1*子空間中的範例應用程式，並注意您會被重新導向至使用 HTTPS，而不會發生任何錯誤。

## <a name="next-steps"></a>後續步驟

了解 Azure Dev Spaces 如何協助您跨多個容器開發更複雜的應用程式，以及如何藉由在不同的空間中使用不同的程式碼版本或分支，來簡化共同開發。

> [!div class="nextstepaction"]
> [在 Azure Dev Spaces 中進行小組開發][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml