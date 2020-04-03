---
title: 為 Azure Kubernetes Service (AKS) 叢集建立 HTTPS 輸入
description: 瞭解如何安裝和配置 NGINX 入口控制器,該控制器使用「讓我們加密」在 Azure Kubernetes 服務 (AKS) 群集中自動生成 TLS 證書。
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 4d0edcac86bebb77495907ec43debf077448b8ac
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617235"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service (AKS) 上建立 HTTPS 輸入控制器

輸入控制器是一項可為 Kubernetes 服務提供反向 Proxy、可設定的流量路由和 TLS 終止的軟體。 Kubernetes 輸入資源可用來設定個別 Kubernetes 服務的輸入規則和路由。 透過輸入控制器和輸入規則，您可以使用單一 IP 位址將流量路由至 Kubernetes 叢集中的多個服務。

本文示範如何在 Azure Kubernetes Service (AKS) 叢集中部署 [NGINX 輸入控制器][nginx-ingress]。 [cert-manager][cert-manager] 專案會用來自動產生和設定 [Let's Encrypt][lets-encrypt] 憑證。 最後，會有兩個應用程式在 AKS 叢集中執行，且均可透過單一 IP 位址來存取。

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- [建立輸入控制器，其使用 Let's Encrypt 自動產生具有靜態公用 IP 的 TLS 憑證][aks-ingress-static-tls]

## <a name="before-you-begin"></a>開始之前

此文章假設您目前具有 AKS 叢集。 如果您需要 AKS 叢集，請參閱[使用 Azure CLI][aks-quickstart-cli] 或[使用 Azure 入口網站][aks-quickstart-portal]的 AKS 快速入門。

本文還假定您具有與 AKS 群組在同一資源群組中具有[DNS 區域][dns-zone]的[自訂網域][custom-domain]。

本文使用 Helm 來安裝 NGINX 輸入控制器、cert-manager 及範例 Web 應用程式。 請確定您使用的是 Helm 的最新版本。 有關升級說明,請參閱[Helm 安裝文件][helm-install]。有關設定和使用 Helm 的詳細資訊,請參閱在[Azure 庫伯奈斯服務 (AKS) 中使用 Helm 安裝應用程式][use-helm]。

本文還要求您運行 Azure CLI 版本 2.0.64 或更高版本。 執行 `az --version` 以尋找版本。 如果需要安裝或升級,請參閱[安裝 Azure CLI][azure-cli-install]。

## <a name="create-an-ingress-controller"></a>建立輸入控制器

要建立入口控制器,請使用指令`helm`安裝*nginx 。* 為了新增備援，您必須使用 `--set controller.replicaCount` 參數部署兩個 NGINX 輸入控制器複本。 為充分享有執行輸入控制器複本的好處，請確定 AKS 叢集中有多個節點。

輸入控制器也需要在 Linux 節點上排程。 Windows 伺服器節點(目前在 AKS 中處於預覽狀態)不應運行入口控制器。 您可以使用 `--set nodeSelector` 參數來指定節點選取器，以告知 Kubernetes 排程器在 Linux 式節點上執行 NGINX 輸入控制器。

> [!TIP]
> 下面的示例為名為入口*基本的*入口資源創建一個 Kubernetes 命名空間。 根據需要為您自己的環境指定命名空間。

> [!TIP]
> 如果要為群集中容器的請求啟用[用戶端源 IP 保留][client-source-ip]`--set controller.service.externalTrafficPolicy=Local`,請添加到 Helm 安裝命令。 用戶端來源 IP 儲存在*X-前轉 -for*下的請求標頭中。 當使用啟用用戶端源 IP 保留的入口控制器時,SSL 傳遞將不起作用。

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

在安裝期間，會為輸入控制器建立 Azure 公用 IP 位址。 在輸入控制器的生命週期內，此公用 IP 位址都是靜態的。 如果您刪除輸入控制器，公用 IP 位址指派將會遺失。 如果您後續又建立其他輸入控制器，將會指派新的公用 IP 位址。 如果您想要持續使用公用 IP 位址，您可以改為[使用靜態公用 IP 位址建立輸入控制器][aks-ingress-static-tls]。

若要取得公用 IP 位址，請使用 `kubectl get service` 命令。 將 IP 位址指派給服務需要幾分鐘的時間。

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

尚未建立任何輸入規則。 如果您瀏覽至公用 IP 位址，即會顯示 NGINX 輸入控制器的預設 404 頁面。

## <a name="add-an-a-record-to-your-dns-zone"></a>將 A 紀錄加入 DNS 區域

使用[az 網路 dns 記錄設定附加記錄][az-network-dns-record-set-a-add-record],使用 NGINX 服務的外部 IP 位址將*A*記錄添加到 DNS 區域。

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name '*' \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> 或者,您可以為入口控制器 IP 位址配置 FQDN,而不是自訂網域。 請注意,此示例適用於 Bash 外殼。
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>安裝 cert-manager

NGINX 輸入控制器支援 TLS 終止。 有數種方式可擷取和設定 HTTPS 的憑證。 本文示範如何使用 [cert-manager][cert-manager]，其會提供自動化的 [Lets Encrypt][lets-encrypt] 憑證產生與管理功能。

要安裝憑證管理員控制器:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

如需 cert-manager 設定的詳細資訊，請參閱 [cert-manager 專案][cert-manager]。

## <a name="create-a-ca-cluster-issuer"></a>建立 CA 叢集簽發者

簽發憑證之前，cert-manager 需要 [Issuer][cert-manager-issuer] 或 [ClusterIssuer][cert-manager-cluster-issuer] 資源。 這些 Kubernetes 資源在功能上完全相同，但是 `Issuer` 會在單一命名空間中運作，而 `ClusterIssuer` 會跨所有命名空間運作。 如需詳細資訊，請參閱 [cert-manager issuer][cert-manager-issuer] 文件。

使用下列範例資訊清單來建立叢集簽發者，例如 `cluster-issuer.yaml`。 利用您組織中的有效電子郵件地址來更新電子郵件地址：

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

若要建立簽發者，請使用 `kubectl apply` 命令。

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>執行示範應用程式

輸入控制器和憑證管理解決方案皆已設定。 現在讓我們在您的 AKS 叢集中執行兩個示範應用程式。 在此示例中,Helm 用於部署簡單*Hello world*應用程式的兩個實例。

在安裝範例 Helm 圖表之前,請將 Azure 範例儲存庫添加到 Helm 環境。

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

使用*azure 範例/aks-helloworld*赫爾姆圖創建名為*aks-helloworld*的演示應用程式。

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

創建名為*aks-helloworld-2*的演示應用程式的第二個實例。 指定一個新的標題和唯一的服務名稱,以便使用 *-set*使兩個應用程式在視覺上不同。

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>建立輸入路由

這兩個應用程式現在都已在您的 Kubernetes 叢集上執行。 但是,它們配置了類型的`ClusterIP`服務,並且無法從 Internet 訪問。 若要使其可公開使用，請建立 Kubernetes 輸入資源。 輸入資源會設定將流量路由至這兩個應用程式之一的規則。

在下面的示例中,位址的流量*hello 世界入口。MY_CUSTOM_DOMAIN*路由到*aks-helloworld*服務。 到位址的流量*hello 世界入口。MY_CUSTOM_DOMAIN/hello-world-2*被路由到*aks-helloworld-2*服務。 到*你好世界入口的交通。MY_CUSTOM_DOMAIN/靜態*路由到名為*aks-helloworld*的服務,用於靜態資產。

使用以下範例`hello-world-ingress.yaml`YAML 建立名為的檔案。 將 *hosts* 和 *host* 更新為您在上一個步驟中建立的 DNS 名稱。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

使用 `kubectl apply` 命令建立輸入資源。

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>驗證已建立憑證物件

接下來，必須建立憑證資源。 憑證資源會定義所需的 X.509 憑證。 有關詳細資訊,請參閱[憑證管理員憑證][cert-manager-certificates]。 Cert 管理員已使用入口-shim 自動為您創建一個證書物件,該物件自 v0.2.2 起自動與證書管理器一起部署。 如需詳細資訊請，請參閱 [ingress-shim 文件][ingress-shim] \(英文\)。

要驗證證書是否成功創建,請使用`kubectl get certificate --namespace ingress-basic`命令並驗證*READY*為*True*,這可能需要幾分鐘時間。

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>測試輸入組態

打開 Web 瀏覽器以*進入 hello 世界。MY_CUSTOM_DOMAIN*庫伯內斯入口控制器。 請注意,您將重定向到使用 HTTPS,證書受信任,演示應用程式顯示在 Web 瀏覽器中。 添加 */hello-world-world-2*路徑,並注意到顯示帶有自定義標題的第二個演示應用程式。

## <a name="clean-up-resources"></a>清除資源

本文使用 Helm 來安裝輸入元件、憑證及範例應用程式。 部署 Helm 圖表時會建立一些 Kubernetes 資源。 這些資源包含 Pod、部署和服務。 要清理這些資源,可以刪除整個範例命名空間或單個資源。

### <a name="delete-the-sample-namespace-and-all-resources"></a>刪除範例命名空間與所有資源

要刪除整個範例命名空間,請使用指令`kubectl delete`並指定命名空間名稱。 命名空間中的所有資源都將被刪除。

```console
kubectl delete namespace ingress-basic
```

然後,刪除 AKS hello 世界應用程式的 Helm 回購:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>單獨刪除資源

或者,更精細的方法是刪除創建的單個資源。 首先,刪除群組頒發者資源:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

使用`helm list`命令列出 Helm 版本。 尋找名為nginx-ingress** 和 aks-helloworld** 的圖表，如下列範例輸出所示：

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

使用 `helm delete` 命令刪除版本。 下列範例會刪除 NGINX 輸入部署和兩個範例 AKS hello world 應用程式。

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

接下來，移除 AKS hello world 應用程式的 Helm 存放庫：

```console
helm repo remove azure-samples
```

移除將流量導向範例應用程式的輸入路由：

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

最後,您可以刪除自身命名空間。 使用`kubectl delete`指令並指定命名空間名稱:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>後續步驟

本文包含 AKS 的一些外部元件。 若要深入了解這些元件，請參閱下列專案頁面：

- [Helm CLI][helm-cli]
- [NGINX 輸入控制器][nginx-ingress]
- [cert-manager][cert-manager]

您也可以：

- [建立具有外部網路連線的基本輸入控制器][aks-ingress-basic]
- [啟用 HTTP 應用程式路由附加元件][aks-http-app-routing]
- [建立使用內部私人網路和 IP 位址的輸入控制器][aks-ingress-internal]
- [建立使用自有 TLS 憑證的輸入控制器][aks-ingress-own-tls]
- [建立輸入控制器，其使用 Let's Encrypt 自動產生具有靜態公用 IP 的 TLS 憑證][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
